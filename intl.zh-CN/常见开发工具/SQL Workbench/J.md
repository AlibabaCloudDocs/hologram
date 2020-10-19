---
keyword: [SQL Workbench/J, Hologres, 开发工具]
---

# SQL Workbench/J

本文为您介绍如何使用交互式分析Hologres连接SQL Workbench/J实现数据的可视化分析。

-   单击[SQL Workbench/J](https://www.sql-workbench.eu/downloads.html)，下载并安装SQL Workbench。
-   开通Hologres实例，详情请参见[购买Hologres](/intl.zh-CN/准备工作/购买Hologres.md)。

SQL Workbench/J是一款免费的、跨平台的SQL查询分析工具，您可以使用SQL Workbench/J通过PostgreSQL驱动连接Hologres进行数据开发。

1.  启动SQL Workbench/J，新建至Hologres的连接。

2.  配置连接信息。

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/2053140061/p65594.png)

    参数说明如下表所示。

    |参数|描述|示例|
    |--|--|--|
    |Driver|PostgreSQL连接Hologres使用PostgreSQL驱动。

|无|
    |URL|`jdbc:postgresql://endpoint:port/database`    -   Endpoint：Hologres实例的公共网络地址。

进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取Endpoint。

    -   Port：Hologres实例的公共网络端口。

进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取Port。

    -   Database：需要连接的Hologres数据库名称。

进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**DB管理**获取数据库名称。

|`jdbc:postgresql://holodemo-cn-hangzhou.aliyun.com:80/postgres`示例仅供参考，实际连接时需要更换为对应项目的实际参数。 |
    |Username|当前Hologres账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取用户的AccessKey ID。

|无|
    |Password|当前Hologres账号的AccessKey Secret。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。

|无|

3.  设置扩展属性。

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1246322061/p65608.png)

    1.  单击**Extended Properties**，配置SSL为true。

    2.  单击**OK**。

4.  单击**OK**，成功连接Hologres至SQL Workbench/J。

    您可以使用SQL Workbench/J查询分析Hologres的数据。更多关于SQL Workbench/J的驱动配置以及软件的安装，请参见[SQL Workbench](https://www.sql-workbench.eu/getting-started.html)。


