---
title: "Eucalyptus 5 Route53 preview"
date: 2020-04-10T12:00:00-07:00
showDate: true
---

When you install a Eucalyptus 5 beta using the new
[ansible faststart](https://blog.eucalyptus.cloud/post/ansible-faststart/)
you may notice that there is a new `route53` service registered.

This new service is a technical preview and can be enabled in any deployment
of Eucalyptus 5 from master by setting a flag in `eucalyptus.conf`:

{{< highlight shell >}}
CLOUD_OPTS="... -Denable.route53.tech.preview=true ..."
{{< / highlight >}}

The route53 preview is integrated with `cloudformation` and `loadbalancing`
so you can automate deployments and use friendly names with load balancer
endpoints.

Cloud administrators now also have the option to enhance the standard eucalyptus
endpoints with addition A or CNAME records.

## Eualyptus Route53 support

Eucalyptus cloudformation supports creation of public and private hosted zones:

{{< highlight yaml >}}
AWSTemplateFormatVersion: 2010-09-09
Description: Route53 public HostedZone
Parameters:
  Zone:
    Description: The zone to create
    Type: String
    Default: example.com
Resources:
  HostedZone:
    Type: AWS::Route53::HostedZone
    Properties:
      HostedZoneConfig:
        Comment: !Sub "Public zone for ${Zone}"
      Name: !Ref Zone
Outputs:
  HostedZoneId:
    Description: The identifier for the public hosted zone
    Value: !Ref HostedZone
  HostedZoneNameServers:
    Description: The nameservers for the public hosted zone
    Value: !Join [", ", !GetAtt HostedZone.NameServers]
{{< / highlight >}}

And management of resource record sets for hosted zones:

{{< highlight yaml >}}
  ARecordSet:
    Type: AWS::Route53::RecordSet
    DependsOn: HostedZone
    Properties:
      HostedZoneName: !Ref ZoneName
      Name: !Sub "name.${ZoneName}"
      ResourceRecords:
      - "10.20.30.45"
      TTL: 300
      Type: A

  CNAMERecordSet:
    Type: AWS::Route53::RecordSet
    DependsOn: HostedZone
    Properties:
      HostedZoneName: !Ref ZoneName
      Name: !Sub "cname.${ZoneName}"
      ResourceRecords:
      - name
      TTL: 300
      Type: CNAME

  AliasRecordSet:
    Type: AWS::Route53::RecordSet
    DependsOn: ARecordSet
    Properties:
      HostedZoneName: !Ref ZoneName
      Name: !Sub "alias.${ZoneName}"
      AliasTarget:
        DNSName: !Sub "name.${ZoneName}"
        EvaluateTargetHealth: no
        HostedZoneId: !Ref HostedZone
      Type: A
{{< / highlight >}}

The above example shows creating an A record and CNAME/alias that reference it.

## Eucalyptus console URL

The Eucalyptus console can be deployed in the cloud or on hardware. The
route53 preview allows a dns name to be added for the console.

{{< highlight yaml >}}
AWSTemplateFormatVersion: 2010-09-09
Description: >-
  Route53 HostedZone and management console dns
  for Eucalyptus
Parameters:
  SystemDnsDomain:
    Description: The system dns domain in use
    Type: String
Resources:
  HostedZone:
    Type: AWS::Route53::HostedZone
    Properties:
      HostedZoneConfig:
        Comment:
      Name: !Ref SystemDnsDomain
  RecordSet:
    Type: AWS::Route53::RecordSet
    Properties:
      HostedZoneId: !Ref HostedZone
      Name: !Sub "console.${SystemDnsDomain}"
      ResourceRecords:
      - !Sub "compute.${SystemDnsDomain}"
      TTL: 300
      Type: CNAME
Outputs:
  HostedZoneId:
    Description: The identifier for the system hosted zone
    Value: !Ref HostedZone
{{< / highlight >}}

Assuming you delegated _mycloud.example.com_ dns to the cloud you would deploy the above using:

{{< highlight shell >}}
euform-create-stack \
    --template-file route53.yaml \
    -p SystemDnsDomain=mycloud.example.com \
    r53-stack
{{< / highlight >}}

To allow console access via _https\://console.mycloud.example.com_
