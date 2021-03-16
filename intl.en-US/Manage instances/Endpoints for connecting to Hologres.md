---
keyword: [Hologres, Alibaba Cloud regions, Domains]
---

# Endpoints for connecting to Hologres

Hologres provides various endpoints for you to connect to Hologres instances that reside in different regions of Alibaba Cloud.

## Available endpoints

The following tables describe the endpoints that you can use to connect to Hologres instances in different Alibaba Cloud regions and network environments.

|地域|服务所在城市|公共网络Endpoint|
|--|------|------------|
|华东1|杭州|`<instanceID>-cn-hangzhou.hologres.aliyuncs.com:80`|
|华北2|北京|`<instanceID>-cn-beijing.hologres.aliyuncs.com:80`|
|华东2|上海|`<instanceID>-cn-shanghai.hologres.aliyuncs.com:80`|
|华南1|深圳|`<instanceID>-cn-shenzhen.hologres.aliyuncs.com:80`|

|Region|City|Public endpoint|
|------|----|---------------|
|China \(Hong Kong\)|Hong Kong \(China\)|`<instanceID>-cn-hongkong.hologres.aliyuncs.com:80`|
|Singapore|Singapore|`<instanceID>-ap-southeast-1.hologres.aliyuncs.com:80`|
|Malaysia \(Kuala Lumpur\)|Kuala Lumpur|`<instanceID>-ap-southeast-3.hologres.aliyuncs.com:80`|
|US \(Silicon Valley\)|Silicon Valley|`<instanceID>-us-west-1.hologres.aliyuncs.com:80`|
|Indonesia \(Jakarta\)|Jakarta|`<instanceID>-ap-southeast-5.hologres.aliyuncs.com:80`|
|India \(Mumbai\)|Mumbai|`<instanceID>-ap-south-1.hologres.aliyuncs.com:80`|

|地域|服务所在城市|经典网络Endpoint|
|--|------|------------|
|华东1|杭州|`<instanceID>-cn-hangzhou-internal.hologres.aliyuncs.com:80`|
|华北2|北京|`<instanceID>-cn-beijing-internal.hologres.aliyuncs.com:80`|
|华东2|上海|`<instanceID>-cn-shanghai-internal.hologres.aliyuncs.com:80`|
|华南1|深圳|`<instanceID>-cn-shenzhen-internal.hologres.aliyuncs.com:80`|

|Region|City|Classic network endpoint|
|------|----|------------------------|
|China \(Hong Kong\)|Hong Kong \(China\)|`<instanceID>-cn-hongkong-internal.hologres.aliyuncs.com:80`|
|Singapore|Singapore|`<instanceID>-ap-southeast-1-internal.hologres.aliyuncs.com:80`|
|Malaysia \(Kuala Lumpur\)|Kuala Lumpur|`<instanceID>-ap-southeast-3-internal.hologres.aliyuncs.com:80`|
|US \(Silicon Valley\)|Silicon Valley|`<instanceID>-us-west-1-internal.hologres.aliyuncs.com:80`|
|Indonesia \(Jakarta\)|Jakarta|`<instanceID>-ap-southeast-5-internal.hologres.aliyuncs.com:80`|
|India \(Mumbai\)|Mumbai|`<instanceID>-ap-south-1-internal.hologres.aliyuncs.com:80`|

|地域|服务所在城市|VPC网络Endpoint|
|--|------|-------------|
|华东1|杭州|`<instanceID>-cn-hangzhou-vpc.hologres.aliyuncs.com:80`|
|华北2|北京|`<instanceID>-cn-beijing-vpc.hologres.aliyuncs.com:80`|
|华东2|上海|`<instanceID>-cn-shanghai-vpc.hologres.aliyuncs.com:80`|
|华南1|深圳|`<instanceID>-cn-shenzhen-vpc.hologres.aliyuncs.com:80`|

|Region|City|VPC endpoint|
|------|----|------------|
|China \(Hong Kong\)|Hong Kong \(China\)|`<instanceID>-cn-hongkong-vpc.hologres.aliyuncs.com:80`|
|Singapore|Singapore|`<instanceID>-ap-southeast-1-vpc.hologres.aliyuncs.com:80`|
|Malaysia \(Kuala Lumpur\)|Kuala Lumpur|`<instanceID>-ap-southeast-3-vpc.hologres.aliyuncs.com:80`|
|US \(Silicon Valley\)|Silicon Valley|`<instanceID>-us-west-1-internal.hologres.aliyuncs.com:80`|
|Indonesia \(Jakarta\)|Jakarta|`<instanceID>-ap-southeast-5-vpc.hologres.aliyuncs.com:80`|
|India \(Mumbai\)|Mumbai|`<instanceID>-ap-south-1-vpc.hologres.aliyuncs.com:80`|

## Sample endpoints

<instanceID\> specifies the ID of an instance. You can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance) to obtain the ID from the instance details page, as shown in the following figure.

![723](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5167966951/p95236.png)

![Instance ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4376085161/p239946.png)

Assume that you have purchased a Hologres instance named testdemo in the 北京Singapore region. The ID of the instance is `hgprecn-cn-xxxk3ovx003`. You can use the following endpoints to connect to the Hologres instance in different network environments:

-   公共网络为`hgprecn-cn-xxxk3ovx003-cn-beijing.hologres.aliyuncs.com:80`。
-   经典网络为`hgprecn-cn-xxxk3ovx003-cn-beijing-internal.hologres.aliyuncs.com:80`。
-   VPC网络为`hgprecn-cn-xxxk3ovx003-cn-beijing-vpc.hologres.aliyuncs.com:80`。

-   Public endpoint: `hgprecn-cn-xxxk3ovx003-ap-southeast-1.hologres.aliyuncs.com`.
-   Classic network endpoint: `hgprecn-cn-xxxk3ovx003-ap-southeast-1-internal.hologres.aliyuncs.com`.
-   VPC endpoint: `hgprecn-cn-xxxk3ovx003-ap-southeast-1-vpc.hologres.aliyuncs.com`.

