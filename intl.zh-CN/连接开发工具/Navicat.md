---
keyword: [Navicat, 连接Hologres, 数据开发]
---

# Navicat

Navicat可以在一个应用程序中连接多个数据库，帮助您轻松创建、管理和维护数据库。本文以Navicat for PostgreSQL为例，为您介绍Navicat如何连接Hologres并进行数据开发。

-   单击[Navicat](https://www.navicat.com.cn/products)，下载并安装Navicat客户端。本次示例使用Navicat 15 for PostgreSQL。
-   开通Hologres实例，详情请参见[购买Hologres](/intl.zh-CN/准备工作/购买Hologres.md)。

1.  新建至Hologres的连接。

    在Navicat客户端，单击顶部菜单栏的**连接**，选择**PostgreSQL**。

2.  配置**PostgreSQL-新建连接**对话框的参数。

    参数说明如下表所示。

    |参数|描述|示例|
    |--|--|--|
    |连接名|自定义的连接名称。|无|
    |主机|连接的Hologres公共网络地址。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取主机。

|holodemo-cn-hangzhou.hologres.aliyuncs.com|
    |端口|连接的Hologres公共网络端口。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取端口。

|80|
    |初始数据库|需要连接的Hologres数据库名称。|postgres|
    |用户名|当前Hologres账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取用户的AccessKey ID。

|无|
    |密码|当前Hologres账号的AccessKey Secret。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。

|无|
    |测试连接|单击**测试连接**，弹出**连接成功**对话框，则表示Navicat成功连接Hologres。|无|

3.  单击**确定**。

4.  进行数据开发。

    Navicat成功连接Hologres后，您可以进行数据开发，详情请参见[Navicat帮助文档](https://www.navicat.com.cn/manual/online_manual/cn/navicat/mac_manual/#/postgresql_database)。Hologres的典型应用场景如下：

    -   直接加速查询离线数据，详情请参见[通过创建外部表加速查询MaxCompute数据](/intl.zh-CN/数据接入/离线同步/MaxCompute/通过创建外部表加速查询MaxCompute数据.md)。

