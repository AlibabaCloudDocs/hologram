---
keyword: [Hologres, Alibaba Cloud regions, Domains]
---

# Endpoints for connecting to Hologres

Hologres provides region-specific endpoints for you to connect to Hologres instances that reside in different regions of Alibaba Cloud.

## Available endpoints

The following tables describe the endpoints that you can use to connect to Hologres instances in different Alibaba Cloud regions and network environments.

|Region|City|Public endpoint|
|------|----|---------------|
|China \(Hong Kong\)|Hong Kong \(China\)|`<instanceID>-cn-hongkong.hologres.aliyuncs.com:80`|
|Singapore \(Singapore\)|Singapore|`<instanceID>-ap-southeast-1.hologres.aliyuncs.com:80`|
|Malaysia \(Kuala Lumpur\)|Kuala Lumpur|`<instanceID>-ap-southeast-3.hologres.aliyuncs.com:80`|
|US \(Silicon Valley\)|Silicon Valley|`<instanceID>-us-west-1.hologres.aliyuncs.com:80`|
|Indonesia \(Jakarta\)|Jakarta|`<instanceID>-ap-southeast-5.hologres.aliyuncs.com:80`|
|India \(Mumbai\)|Mumbai|`<instanceID>-ap-south-1.hologres.aliyuncs.com:80`|
|Japan \(Tokyo\)|Tokyo|`<instanceID>-ap-northeast-1.hologres.aliyuncs.com:80`|

|Region|City|Classic network endpoint|
|------|----|------------------------|
|China \(Hong Kong\)|Hong Kong \(China\)|`<instanceID>-cn-hongkong-internal.hologres.aliyuncs.com:80`|
|Singapore \(Singapore\)|Singapore|`<instanceID>-ap-southeast-1-internal.hologres.aliyuncs.com:80`|
|Malaysia \(Kuala Lumpur\)|Kuala Lumpur|`<instanceID>-ap-southeast-3-internal.hologres.aliyuncs.com:80`|
|US \(Silicon Valley\)|Silicon Valley|`<instanceID>-us-west-1-internal.hologres.aliyuncs.com:80`|
|Indonesia \(Jakarta\)|Jakarta|`<instanceID>-ap-southeast-5-internal.hologres.aliyuncs.com:80`|
|India \(Mumbai\)|Mumbai|`<instanceID>-ap-south-1-internal.hologres.aliyuncs.com:80`|
|Japan \(Tokyo\)|Tokyo|`<instanceID>-ap-northeast-1-internal.hologres.aliyuncs.com:80`|

|Region|City|VPC endpoint|
|------|----|------------|
|China \(Hong Kong\)|Hong Kong \(China\)|`<instanceID>-cn-hongkong-vpc.hologres.aliyuncs.com:80`|
|Singapore \(Singapore\)|Singapore|`<instanceID>-ap-southeast-1-vpc.hologres.aliyuncs.com:80`|
|Malaysia \(Kuala Lumpur\)|Kuala Lumpur|`<instanceID>-ap-southeast-3-vpc.hologres.aliyuncs.com:80`|
|US \(Silicon Valley\)|Silicon Valley|`<instanceID>-us-west-1-internal.hologres.aliyuncs.com:80`|
|Indonesia \(Jakarta\)|Jakarta|`<instanceID>-ap-southeast-5-vpc.hologres.aliyuncs.com:80`|
|India \(Mumbai\)|Mumbai|`<instanceID>-ap-south-1-vpc.hologres.aliyuncs.com:80`|
|Japan \(Tokyo\)|Tokyo|`<instanceID>-ap-northeast-1-vpc.hologres.aliyuncs.com:80`|

## Sample endpoints

<instanceID\> indicates the ID of an instance. You can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance) to obtain the ID from the instance details page, as shown in the following figure.

![Instance ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4376085161/p239946.png)

For example, you have purchased a Hologres instance named testdemo in the Singapore \(Singapore\) region. The ID of the instance is `hgprecn-cn-xxxk3ovx003`. You can use the following endpoints to connect to the Hologres instance in different network environments:

-   Public endpoint: `hgprecn-cn-xxxk3ovx003-ap-southeast-1.hologres.aliyuncs.com:80`.
-   Classic network endpoint: `hgprecn-cn-xxxk3ovx003-ap-southeast-1-internal.hologres.aliyuncs.com:80`.
-   VPC endpoint: `hgprecn-cn-xxxk3ovx003-ap-southeast-1-vpc.hologres.aliyuncs.com:80`.

