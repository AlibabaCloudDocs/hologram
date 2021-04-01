---
keyword: [购买实例, Hologres]
---

# 购买Hologres

使用交互式分析Hologres之前，您需要购买Hologres实例。本文为您介绍如何使用阿里云账号购买Hologres实例。

-   阿里云账号注册，详情请参见[t12832.md\#]()。
-   实名认证，详情请参见[t12833.md\#]()或[t12834.md\#]()。

系统默认设置购买实例的阿里云主账号为超级管理员Superuser。Superuser拥有该实例的所有权限。

RAM用户账号经过阿里云主账号授权后才能购买实例，详情请参见[授予RAM用户权限](/cn.zh-CN/账号与权限管理/授予RAM用户权限.md)。RAM用户的购买流程与主账号一致。

1.  使用阿里云主账号登录[阿里云官网](https://www.aliyun.com/)。

2.  进入[Hologres产品详情页](https://www.aliyun.com/product/hologram?spm=5176.224200.h2v3icoap.186.58e16ed61Zjftc)。

3.  单击**立即开通**。

4.  选择付费模式并配置相应参数。

    Hologres根据存储资源和计算资源进行收费，包括**包年包月**和**按量付费**两种付费模式，详情请参见[计费方式](/cn.zh-CN/产品计费/计费方式.md)。两种付费模式的说明如下：

    -   **包年包月**

        根据实际业务需求，估算所需要的计算资源和存储资源，采用预先付费的方式使用Hologres，详情请参见[包年包月](/cn.zh-CN/产品计费/包年包月.md)。

        ![包年包月](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2728517161/p135606.png)

        **说明：**

        -   如果您需要的计算资源规格超过**512核2048GB**，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)购买。
        -   使用**包年包月**付费模式时，如果存储资源超过额度，则系统自动为您转为**按量付费**。
    -   **按量付费**

        计算费用根据购买的计算资源规格，以实例的运行时长收费。存储费用根据实际存储量，以存储的时长收费。每小时结算一次。详情请参见[按量付费](/cn.zh-CN/产品计费/按量付费.md)。

        ![按量付费](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3728517161/p135608.png)

    **说明：** 如果您需要在同一地域购买多个实例，请尽量避免使用相同的实例名称。

5.  单击**立即购买**。

6.  在**确认订单**页面，请仔细核对所购买实例的付费模式、实例名称、资源以及地域等信息。勾选对应的**服务协议**。

7.  单击**去支付**。

8.  在**支付**页面完成付款。

    成功购买实例后，您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)查看实例信息。管理控制台的使用方法请参见[概览](/cn.zh-CN/实例管理/Hologres管理控制台/概览.md)。

    Hologres兼容PostgreSQL，您可以使用psql客户端、ETL（Extract-Transform-Load）或BI（Business Intelligence）工具连接Hologres并进行数据开发，具体如下：

    -   使用psql客户端进行数据开发，请参见[psql客户端快速入门]()。
    -   使用Holostudio进行数据开发，请参见[HoloStudio快速入门](/cn.zh-CN/连接开发工具/DataWorks数仓开发/HoloStudio快速入门.md)。
    -   更多开发工具请参见[概述](/cn.zh-CN/连接开发工具/概述.md)。

