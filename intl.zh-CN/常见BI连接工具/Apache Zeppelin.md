---
keyword: [Hologres, Apache Zeppelin, 可视化分析]
---

# Apache Zeppelin

Apache Zeppelin是一款基于Web的Notebook产品，能够交互式数据分析。使用Zeppelin，您可以使用丰富的预构建语言后端（或解释器）制作交互式的协作文档，例如Scala、Python、SparkSQL、Hive等。Hologres兼容PostgreSQL ，支持直接连接Apache Zeppelin进行数据分析。本文为您介绍Apache Zeppelin如何连接Hologres并可视化分析。

## 使用限制

最新版的Apache Zeppelin中，PostgreSQL解释器已被弃用，并合并到JDBC解释器中。您可以使用具有相同功能的JDBC Interpreter对接Hologres。

## Apache Zeppelin连接Hologres

1.  安装Apache Zeppelin

    安装Apache Zeppelin，详请参见[Apache Zeppelin官方文档](http://zeppelin.apache.org/docs/latest/quickstart/install.html)。

2.  配置解释器

    1.  登录Apache Zeppelin，单击右上方的用户名，从下拉菜单中选择Interpreter。

        ![Interpreter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8702764161/p244967.png)

    2.  在页面右上角单击**+Create**，配置如下参数信息，创建一个新的Interpreter。

        ![Interpreter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8702764161/p244976.png)

        -   Interpreter Name填写您自定义的名称。
        -   在创建时，您需要将Interpreter Group设置为**JDBC**。
    3.  在将Interpreter Group设置为**JDBC**后，您需要配置如下参数信息。

        ![解释器配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8702764161/p244983.png)

        |参数|说明|
        |--|--|
        |default.url|JDBC的URL，填写格式为`jdbc:postgresql://{host}:{port}/{database}`，其中各参数解释如下所示：        -   host：Hologres实例的网络地址。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取网络地址。
        -   port：Hologres实例的端口。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取实例端口。
        -   database：Hologres创建的数据库名称。 |
        |default.user|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/intl.zh-CN/准备工作/准备阿里云账号.md)。|
        |default.password|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/intl.zh-CN/准备工作/准备阿里云账号.md)。|

        您也可以根据业务需求设置页面中的其它参数，更多关于参数的解释说明，请参见[Apache Zeppelin官方文档](http://zeppelin.apache.org/docs/latest/interpreter/jdbc.html)。

    4.  单击页面下方的**Save**保存配置。

3.  新建Notebook进行操作分析。

    在Notebook编写SQL时，必须要在SQL前加入解释器的名称，例如`%hologres`，以便Zepplin使用指定的解释器生成并发送查询。您可以在页面指定数据展示类型，查询之后的结果将展示在页面下方。

    ![notebook](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8702764161/p244992.png)


