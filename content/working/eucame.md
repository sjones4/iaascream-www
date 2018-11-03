---
title: "euca.me"
date: 2018-06-09T23:25:45-07:00
showDate: false
---

[euca.me](https://www.euca.me/) is a site providing a fast start install
for eucalyptus with dns for instances and configuration of your cloud
with a (self signed) certificate that matches the domain.

To install eucalyptus configured for `euca.me` run:

{{< highlight bash >}}
> bash <(curl -Ls https://go.euca.me)
{{< / highlight >}}

There are currently (Nov 2018) also early access builds for eucalyptus
5 available which can be used for evaluation:

{{< highlight bash >}}
> bash <(curl -Ls https://go.euca.me/5ea)
{{< / highlight >}}

These releases are currently alpha quality so some functionality will
not work and development of features is still in progress.

