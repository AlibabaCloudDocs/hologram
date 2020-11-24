---
keyword: [Hologres, regions, Alibaba Cloud Domains]
---

# Endpoints that are used to access Hologres

Hologres provides various endpoints to access Hologres instances that reside in different regions of Alibaba Cloud.

## Available endpoints

The following tables list the endpoints used to access Hologres instances in different Alibaba Cloud regions and network environments.

|Region|City|Endpoint|
|------|----|--------|
|China \(Hong Kong\)|Hong Kong \(China\)|`<instanceID>-cn-hongkong.hologres.aliyuncs.com:80`|
|Singapore \(Singapore\)|Singapore|`<instanceID>-ap-southeast-1.hologres.aliyuncs.com:80`|
|Malaysia \(Kuala Lumpur\)|Kuala Lumpur|`<instanceID>-ap-southeast-3.hologres.aliyuncs.com:80`|
|US \(Silicon Valley\)|Silicon Valley|`<instanceID>-us-west-1.hologres.aliyuncs.com:80`|

|Region|City|Endpoint|
|------|----|--------|
|China \(Hong Kong\)|Hong Kong \(China\)|`<instanceID>-cn-hongkong-internal.hologres.aliyuncs.com:80`|
|Singapore \(Singapore\)|Singapore|`<instanceID>-ap-southeast-1-internal.hologres.aliyuncs.com:80`|
|Malaysia \(Kuala Lumpur\)|Kuala Lumpur|`<instanceID>-ap-southeast-3-internal.hologres.aliyuncs.com:80`|
|US \(Silicon Valley\)|Silicon Valley|`<instanceID>-us-west-1-internal.hologres.aliyuncs.com:80`|

|Region|City|Endpoint|
|------|----|--------|
|China \(Hong Kong\)|Hong Kong \(China\)|`<instanceID>-cn-hongkong-vpc.hologres.aliyuncs.com:80`|
|Singapore \(Singapore\)|Singapore|`<instanceID>-ap-southeast-1-vpc.hologres.aliyuncs.com:80`|
|Malaysia \(Kuala Lumpur\)|Kuala Lumpur|`<instanceID>-ap-southeast-3-vpc.hologres.aliyuncs.com:80`|
|US \(Silicon Valley\)|Silicon Valley|`<instanceID>-us-west-1-internal.hologres.aliyuncs.com:80`|

## Sample endpoints

<instanceID\> specifies the ID of an instance. You can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance) to obtain the ID from the instance details page, as shown in the following figure.

![723](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5167966951/p95236.png)

Assume that you have purchased a Hologres instance named testdemo in the Singpore \(Singpore\) region. The ID of the instance is `hgprecn-cn-xxxk3ovx003`. You can use the following endpoints to access the Hologres instance in different network environments:

-   Public endpoint: `hgprecn-cn-xxxk3ovx003-ap-southeast-1.hologres.aliyuncs.com`.
-   Classic network endpoint: `hgprecn-cn-xxxk3ovx003-ap-southeast-1-internal.hologres.aliyuncs.com`.
-   VPC endpoint: `hgprecn-cn-xxxk3ovx003-ap-southeast-1-vpc.hologres.aliyuncs.com`.

