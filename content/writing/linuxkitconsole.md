---
title: "Eucalyptus Console LinuxKit Image"
date: 2018-12-10T00:00:00-07:00
showDate: true
---

Using the experimental [Fedora 29 rpms]({{< relref "eucalyptus44fedora.md" >}})
we will create a small Docker image for the Eucalyptus console and see
how we can use that standalone, in a Eucalyptus cloud using Fedora
Atomic Host image, and as a custom image using LinuxKit.

## Eucalyptus console as a Docker image
The first step is to build a docker image from the pre-release snapshot
repository for Fedora. A [Dockerfile](https://github.com/sjones4/eucalyptus-extras/blob/master/docker/eucalyptus-console/Dockerfile)
is here which shows the details, the [image](https://hub.docker.com/r/sjones4/eucalyptus-console/)
is available from docker hub.

To run the console standalone you'll need to tell it where your cloud is:

```plain
# cat console-main.ini
[app:main]
use = config:/etc/eucaconsole/console-main-defaults.ini
ufshost = ec2.my-euca-cloud-10-10-10-44.euca.me
```

The above example uses [euca.me](https://www.euca.me/) for DNS.

You can then bring up the console using your configuration file:

```plain
# docker run -p 8888:8888 \
  -v $(pwd)/console-main.ini:/etc/eucaconsole/console-main.ini \
  sjones4/eucalyptus-console:4.4 eucaconsole
```

This works well outside of your Eucalyptus cloud, but for running on the
cloud we'll need a virtual machine image.

We can use `Fedora Atomic Host 29` which comes with Docker support to
easily spin up the console. You can download a suitable Fedora image
here:

{{< highlight plain >}}
# python <(curl -Ls https://eucalyptus.cloud/images)
{{< / highlight >}}

To run the console on an instance use user data:

{{< highlight plain >}}
# cat user-data.yaml
#cloud-config
runcmd:
 - docker run --detach -p 8888:8888 sjones4/eucalyptus-console:4.4 eucaconsole
{{< / highlight >}}

This example brings up the console on port 8888 of the instance. To launch
an instance using this user data:

{{< highlight plain >}}
# euca-run-instances -t m2.xlarge --user-data-file user-data.yaml emi-11111111
#
# euca-authorize -P tcp -p 8888 default
{{< / highlight >}}

Where `emi-11111111` is the identifier for your Fedora image. Don't
forget to allow access on port 8888.

Running the console on an instance using `Fedora Atomic Host 29` is
convenient but the image size is large, and downloading the Docker image
on instance start can be slow. Can we do better?

## A LinuxKit console image
[LinuxKit](https://github.com/linuxkit/linuxkit) allows you to build
small virtual machine images composed from Docker images. We can use the
Docker image we created earlier as part of a minimal image. We'll need a
[yaml descriptor](https://github.com/sjones4/eucalyptus-extras/blob/master/docker/eucalyptus-console/eucalyptus-console.yaml)
for our image.

To build the image with LinuxKit from the descriptor, use:

{{< highlight plain >}}
# linuxkit build -format raw-bios eucalyptus-console.yaml
{{< / highlight >}}

Once built you can install the image on your Eucalyptus cloud using:

{{< highlight plain >}}
# truncate --size 1G eucalyptus-console-bios.img
# euca-install-image -n eucaconsole --virt hvm --arch x86_64 -b linuxkit -i eucalyptus-console-bios.img
{{< / highlight >}}

The truncate command increases the image size. Running an instance with
the console is simplified as the image has everything we need:

{{< highlight plain >}}
# euca-run-instances -t m2.xlarge emi-22222222
{{< / highlight >}}

This custom console image is under 250MiB uncompressed, so is fast to
launch.

## Got anything else?
Need even more ways to run the console? take a look at the [snap](https://snapcraft.io/eucalyptus-console)





