---
keyword: [Hologres, 管理控制台权限]
---

# RAM用户授权相关

Hologres管理控制台主要集成了RAM鉴权和实例的部分开发权限，本文内容将为您介绍Hologres管理控制台权限相关的常见问题解决方法。

## 问题汇总

您需要具备Hologres管理控制台操作权限，才能管理Hologres实例时。与Hologres管理控制台权限相关的常见问题，请单击如下链接查看解决方法。

-   [RAM用户无法查看实例列表及实例ID](#section_te9_8bd_4iy)
-   [RAM用户无实例的操作权限](#section_ovp_spc_a51)

## RAM用户无法查看实例列表及实例ID

-   问题现象

    RAM用户选择了正确的地域，但无法看到已购买的实例，并提示**暂无权限查看所有实例，请让主账号前往RAM中心授予当前用户“xxx/\*”资源的“hologram:ListInstances”权限**报错。

-   问题原因

    当前RAM用户没有查看实例列表的权限。

-   解决方法

    主账号登录[RAM控制台](https://ram.console.aliyun.com/overview)，授予RAM用户显示实例列表的权限**AliyunHologresReadOnlyAccess**。


## RAM用户无实例的操作权限

-   问题现象

    被授权为Superuser的RAM用户无法购买、升配和降配实例，以及转换实例的付费方式由**按量付费**为**订阅**。提示**RAM子账号鉴权不通过**报错。

-   问题原因

    购买、升配、降配及转换付费方式等操作涉及费用帐单，由主账号统一控制，当前RAM用户没有相关权限。

-   解决方法

    主账号登录[RAM控制台](https://ram.console.aliyun.com/overview)，授予RAM用户与实例费用相关的权限**AliyunHologresFullAccess**及**AliyunBSSOrderAccess**。


