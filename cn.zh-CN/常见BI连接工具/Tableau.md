---
keyword: [BI工具, 可视化分析, Tableau]
---

# Tableau

本文为您介绍Tableau如何连接Hologres并可视化分析数据。

Tableau是安全并且灵活的端到端数据分析平台，提供从连接到协作的一整套功能。Hologres兼容PostgreSQL，支持直接连接Tableau并可视化分析数据。

1.  下载并安装Tableau。

    进入[Tableau官网](https://www.tableau.com/zh-cn/products)，根据业务需求下载相应的Tableau客户端，并根据提示安装。本次试验使用[Tableau Desktop](https://www.tableau.com/zh-cn/products/desktop)。

2.  连接Hologres。

    1.  成功安装客户端后，打开客户端。

    2.  在左侧导航栏的**连接** \> **到服务器**区域，选择**PostgreSQL**，配置连接Hologres的信息。

        ![Tableau](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1950420061/p166746.png)

        参数说明如下表所示。

        |参数|描述|
        |--|--|
        |服务器|Hologres实例的公共网络地址。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取公共网络地址。 |
        |端口|Hologres的实例端口。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取端口。 |
        |数据库|Hologres创建的数据库名称。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**DB管理**获取数据库名称。 |
        |身份验证|选择**用户名和密码**。|
        |用户名|当前阿里云账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。 |
        |密码|当前阿里云账号的AccessKey Secret。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。 |
        |需要SSL|不勾选。|

    3.  单击**登录**。

3.  Tableau可视化分析数据。

    使用Tableau成功连接Hologres后，您可以可视化分析已有的表数据，详情请参见[Tableau官网教程](https://www.tableau.com/zh-cn/learn/get-started)。


