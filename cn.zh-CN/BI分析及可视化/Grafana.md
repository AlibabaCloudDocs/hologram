---
keyword: [Grafana, Hologres, 可视化分析]
---

# Grafana

本文为您介绍Grafana如何连接Hologres并可视化分析数据。

1.  安装Grafana

    安装Grafana，详请参见[Grafana官方文档](https://grafana.com/docs/grafana/latest/installation/?pg=docs)。

2.  连接Hologres

    1.  登录Grafana，在左侧导航栏单击![configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3422730261/p271368.png)图标，选择**Data Source**创建一个数据源。

        ![创建数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3422730261/p271370.png)

    2.  在页面中单击**Add data source**进入数据源页面。

        ![数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3422730261/p271376.png)

    3.  在**Add data source**页面上方搜索框中输入**PostgreSQL**，找到PostgreSQL数据源后，单击**Select**。

        ![select](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3422730261/p271387.png)

    4.  根据需求填写PostgreSQL Connection的连接信息。

        ![配置参数](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3422730261/p271396.png)

        具体参数配置如下所示：

        |参数|描述|
        |--|--|
        |Host|Hologres实例的公共网络地址。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取网络地址。|
        |Database|Hologres的数据库名称。|
        |User|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |Password|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |SSL Mode|按需配置，根据您的Endpoint是否开启SSL决定。|
        |Version|PostgreSQL的版本选择11。|
        |更多参数|其余更多参数说明，请参见[Grafana官方文档](https://grafana.com/docs/grafana/latest/datasources/postgres/)。|

    5.  参数填写完成后，单击页面下方的**Save & Test**，如果页面提示`Database Connection OK`则表示可以成功连接。

3.  数据分析

    完成配置之后，您可以根据该数据源进行数据分析，更多关于数据分析的操作请参见[Grafana官方文档](https://grafana.com/docs/grafana/latest/getting-started/)。

    ![数据分析](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3422730261/p271454.png)


