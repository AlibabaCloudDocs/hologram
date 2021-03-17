---
keyword: [Redash, Hologres, 可视化分析]
---

# Redash

Redash是一款开源BI工具，可以使用其进行数据可视化。同时也具备了报警、订阅等功能。Hologres兼容PostgreSQL，支持直接连接Redash进行数据分析。本文为您介绍Redash如何连接Hologres并可视化分析数据。

## 操作步骤

1.  安装Redash

    安装Redash，详请参见[Redash官方文档](https://redash.io/help/open-source/setup)。

2.  连接Hologres

    1.  登录Redash，单击页面右上方![设置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5547764161/p245041.png)的图标，从下拉选项中选择Data Sources。

        ![data source](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1110255161/p248958.png)

    2.  进入**New Data Source**页签，单击**+New Data Source**添加数据源。

        ![添加数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1110255161/p248963.png)

    3.  在弹框页面配置参数后，单击**Create**创建数据连接，您可以在Redash查看Hologres的数据。

        ![添加数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1110255161/p245060.png)

        |参数|说明|
        |--|--|
        |Type Selection|PostgreSQL|添加数据源时，在搜索框中查找PostgreSQL，并单击进行添加。|
        |Configuration|Host|Hologres实例的公共网络地址。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取网络地址。|
        |Port|Hologres实例的端口。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取实例端口。|
        |User|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |Password|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |Database Name|Hologres创建的数据库名称。|

3.  在页面上方选择**Create** \> **New Query**，您可以在页面的编辑框中编写SQL语句。

    ![new query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1268764161/p245059.png)

4.  SQL语句编写完成后，可以根据SQL创建可视化图表。

    ![可视化图表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3088764161/p245062.png)

    更多关于Redash的Query编写事项及创建可视化内容的操作，请参见[Redash官方文档](https://redash.io/help)。


