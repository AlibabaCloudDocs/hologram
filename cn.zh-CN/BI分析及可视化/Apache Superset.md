---
keyword: [Hologres, Apache Superset, 可视化分析]
---

# Apache Superset

本文为您介绍Apache Superset如何连接Hologres并可视化分析数据。

-   开通Hologres，详请参见[购买Hologres](/cn.zh-CN/准备工作/购买Hologres.md)。
-   安装Apache Superset，详请参见[Apache Superset官网](https://superset.apache.org/resources)。

Apache Superset \( incubating \) 是一款数据探索和可视化分析的开源BI工具。Apache Superset基于Pytho开发，使用了Flask、Pandas、SQLAlchemy 等组件。Hologres兼容PostgreSQL，如下示例使用Apache Superset V0.38.0版本连接Apache Superset进行数据分析。

1.  登录Superset，在顶部菜单栏选择**Sources** \> **Databases**，进入Databases页面。

    ![databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7412260161/p224572.png)

2.  在页面右上角单击![添加](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7412260161/p224576.png)按钮，进入Add Databases页面配置如下参数，添加数据源。

    ![添加数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7412260161/p224628.png)

    |参数|说明|
    |--|--|
    |Database|您定义的数据库名称。|
    |SQLAlchemy URI|连接Hologres目标数据库的连接字符串。SQLAlchemy URI具体格式如下：    ```
postgresql+psycopg2://{AccessKey ID}:{AccessKey Secret}@{host}:{port}/{database}
    ```

其中，各参数说明如下所示：    -   AccessKey ID和AccessKey Secret：当前阿里云账号的AccessKey ID和AccessKey Secret。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。
    -   host：Hologres实例的公共网络地址。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取公共网络地址。
    -   port：Hologres实例的端口。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取实例端口。
    -   database：Hologres创建的数据库名称。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从DB管理页签获取数据库名称。
更多有关连接信息的说明，请参见[SQLAlchemy](https://docs.sqlalchemy.org/en/12/core/engines.html#postgresql)。|

3.  单击**Test Connection**按钮以确认可以成功连接和验证。

4.  如果连接正常，请单击页面底部的**Save**按钮保存配置。

    保存完成后，您就可以连接Hologres以可视化的方式分析展示数据，具体操作请参见[Apache Superset](https://superset.apache.org/docs/databases/postgres)。


