---
keyword: [Hologres, region, Alibaba Cloud Domains service]
---

# Endpoints used to access Hologres

Hologres provides different endpoints for you to access Hologres instances that reside in different regions of Alibaba Cloud.

## Available endpoints

The following tables list the endpoints used to access Hologres instances in different regions of Alibaba Cloud and the network environments.

|Region|City|Endpoint|
|------|----|--------|
|China \(Hong Kong\)|Hong Kong \(China\)|`<instanceID>-cn-hongkong.hologres.aliyuncs.com`|
|Singapore \(Singapore\)|Singapore|`<instanceID>-ap-southeast-1.hologres.aliyuncs.com`|
|Malaysia \(Kuala Lumpur\)|Kuala Lumpur|`<instanceID>-ap-southeast-3.hologres.aliyuncs.com`|
|US \(Silicon Valley\)|Silicon Valley|`<instanceID>-us-west-1.hologres.aliyuncs.com`|

|Region|City|Endpoint|
|------|----|--------|
|China \(Hong Kong\)|Hong Kong \(China\)|`<instanceID>-cn-hongkong-internal.hologres.aliyuncs.com`|
|Singapore \(Singapore\)|Singapore|`<instanceID>-ap-southeast-1-internal.hologres.aliyuncs.com`|
|Malaysia \(Kuala Lumpur\)|Kuala Lumpur|`<instanceID>-ap-southeast-3-internal.hologres.aliyuncs.com`|
|US \(Silicon Valley\)|Silicon Valley|`<instanceID>-us-west-1-internal.hologres.aliyuncs.com`|

|Region|City|Endpoint|
|------|----|--------|
|China \(Hong Kong\)|Hong Kong \(China\)|`<instanceID>-cn-hongkong-vpc.hologres.aliyuncs.com`|
|Singapore \(Singapore\)|Singapore|`<instanceID>-ap-southeast-1-vpc.hologres.aliyuncs.com`|
|Malaysia \(Kuala Lumpur\)|Kuala Lumpur \(Malaysia\)|`<instanceID>-ap-southeast-3-vpc.hologres.aliyuncs.com`|
|US \(Silicon Valley\)|Silicon Valley|`<instanceID>-us-west-1-internal.hologres.aliyuncs.com`|

## Sample endpoints

<instanceID\> specifies the ID of an instance. You can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance) to obtain the ID from the instance details page. The following figure shows the related information.

![723](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5167966951/p95236.png)

Assume that youpurchased a Hologres instance named testdemo in Singpore \(Singpore\). The ID of the instance is `hgprecn-cn-xxxk3ovx003`. You can use the following endpoints to access the Hologres instance in different network environments:

-   Public endpoint: `hgprecn-cn-xxxk3ovx003-ap-southeast-1.hologres.aliyuncs.com`.
-   Classic network endpoint: `hgprecn-cn-xxxk3ovx003-ap-southeast-1-internal.hologres.aliyuncs.com`.
-   VPC endpoint: `hgprecn-cn-xxxk3ovx003-ap-southeast-1-vpc.hologres.aliyuncs.com`.

