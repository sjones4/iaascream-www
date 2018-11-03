---
title: "Snaps"
date: 2018-06-09T23:25:45-07:00
showDate: false
---

Making eucalyptus available as snaps.

A snap for [eucalyptus tools](https://snapcraft.io/eucalyptus-tools) is
the first. This snap has all the regular euca2ools commands (aliases
may need to be configured) but also has a `euca` command with basic
bash completion, e.g.

{{< highlight bash >}}
>
> euca ec2 describe-instances
>
> euca cloudwatch list-metrics
> euca cw list-metrics
>
{{< / highlight >}}

Services with long names also have a short alias as shown above.

A snap for [eucalyptus console](https://snapcraft.io/eucalyptus-console)
is in progress and is suitable for personal use for accessing eucalyptus
or aws, see the [github repository](https://github.com/sjones4/eucalyptus-extras/tree/master/snaps/eucalyptus-console)
for details on configuration.

