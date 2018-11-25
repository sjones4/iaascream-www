---
title: "Eucalyptus 4.4 Fedora 29 snapshot releases"
date: 2018-11-22T22:44:44-07:00
showDate: true
---

Snapshot releases for 4.4 on Fedora 29 are now available via `euca.me`
fast start:

{{< highlight bash >}}
> bash <(curl -Ls https://go.euca.me/44fcea)
{{< / highlight >}}

or via package installs (on Fedora 29):

{{< highlight bash >}}
https://downloads.eucalyptus.cloud/software/eucalyptus/snapshot/4.4/fedora/29/x86_64/eucalyptus-release-4.4-2.12.as.fc29.noarch.rpm
{{< / highlight >}}

other than the (above) release rpm, installation can be performed as per
the [4.4.x documentation](http://docs.eucalyptus.cloud/eucalyptus/4.4.4/index.html#install-guide/eucalyptus.html)
skipping the `epel` repository configuration.

Fedora RPMs are built from the same [maint-4.4](https://github.com/corymbia/eucalyptus/tree/maint-4.4)
branch as CentOS releases, using a Fedora [build image](https://hub.docker.com/r/sjones4/eucalyptus-builder/tags/).

Note that eucalyptus 4.4 on Fedora 29 is experimental, but functionality
other than instance migration is expected to work.

