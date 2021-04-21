---
keyword: [SAP BusinessObjects, Hologres, 可视化分析]
---

# SAP BusinessObjects

SAP BusinessObjects（简称SAP BO）可提供报表、仪表盘、即席分析和数据管理功能，是SAP的知名的BI软件。本文将为您介绍如何SAP BusinessObjects连接Hologres并可视化分析数据。

1.  安装PostgreSQL ODBC驱动

    您可以从PostgreSQL官网[下载PostgreSQL ODBC驱动](https://www.postgresql.org/ftp/odbc/versions/msi/)。

    **说明：** 由于Hologres兼容PostgreSQL 11的生态，请您务必在**SAP BusinessObjects服务器端和客户端**安装**psqlodbc\_11\_01\_0000以上的版本**。

2.  创建Relational Connection

    1.  打开SAP Information Tool，选择**File** \> **New Universe**

        ![new universe](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5860998161/p265851.png)

    2.  您可以从已有的Project中选择需要创建连接的Project，单击**Next**。如果当前不存在Project，您也可以选择**Create a project**进行创建。

        ![project](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6860998161/p265854.png)

    3.  选择Relational data source，单击**Next**。

        ![data source type](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6860998161/p265862.png)

    4.  填写Resource Name，单击**Next**。

        ![名称](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6860998161/p266351.png)

    5.  在列表中选择**PostgreSQL** \> **PostgreSQL 11** \> **ODBC Drivers**，单击**Next**。

        ![odbc](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6860998161/p266355.png)

    6.  输入User Name、Password，选择Use connection string，单击Driver后的![刷新](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6860998161/p266361.png)图标，从下拉框中选择目标Driver并配置剩余参数。具体参数说明如下表所示。

        ![参数配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6860998161/p266358.png)

        |参数|说明|
        |--|--|
        |User Name|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |Password|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |Host|Hologres实例的公共网络地址。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取网络地址。|
        |Port|Hologres的实例端口。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取实例端口。|
        |Database|Hologres的数据库名称。|

    7.  单击**Test Connection**，页面提示Test Successful表示连接成功。单击**Next**，进行连接配置和参数配置，如果没有特殊的配置需求，可以直接单击**Next**。

3.  创建Data Foundation

    1.  在New Data Foundation页面，填写Resource Name，单击**Next**。

    2.  在列表中勾选需要加入的表，单击**Next**。

        ![选表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6860998161/p266409.png)

4.  创建Relational Business Layer

    在New Relational Business Layer页面，填写Resource Name，单击**Finish**。执行完成后，即创建了SAP BusinessObjects到Hologres的连接，您就可以开始进行模型设计，创建可视化分析等操作。


