---
keyword: [Hologres, Davinci, 可视化分析, BI工具]
---

# Davinci

Davinci是宜信数据团队开源的一款商业智能（Business Intelligence）产品。本文将为您介绍Davinci如何连接Hologres并进行可视化分析。

## 注意事项

Davinci默认不读取PostgreSQL中外部表的表结构，所以暂时无法在页面中查询到外部表元数据，但是您可以直接使用SQL语句进行查询。

![SQL语句](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3112949161/p268668.png)

1.  安装Davinci

    安装Davinci，详请参见[Davinci官方文档](https://edp963.github.io/davinci/docs/zh/1.1-deployment)。

2.  安装JDBC驱动

    1.  请前往PostgreSQL官网[下载](https://mvnrepository.com/artifact/org.postgresql/postgresql)42.2.18以上版本的JDBC驱动。

    2.  将下载的PostgreSQL的JDBC驱动包放置于<Davinci安装目录\>\\lib目录下。

    3.  Davinci默认支持的不是PostgreSQL数据源，您需要配置自定义数据源。进入<Davinci安装目录\>\\lib目录，运行如下命令，创建一个自定义数据源配置文件。

        ```
        mv datasource_driver.yml.example datasource_driver.yml
        ```

    4.  编辑该文件，添加如下内容，配置PostgreSQL数据源。

        ```
        postgresql:
            name: postgresql
            desc: postgresql
            version:
            driver: org.postgresql.Driver
            keyword_prefix: \"
            keyword_suffix: \"
            alias_prefix: \"
            alias_suffix: \"
        ```

    5.  重启Davinci，完成JDBC驱动安装和PostgreSQL数据源配置。

3.  连接Hologres

    1.  登录Davinci，单击页面右上角的**+创建**，创建一个项目。

    2.  创建项目完成后，单击进入项目。在左侧导航栏单击![数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3112949161/p268630.png)图标，进入数据源页面。单击右上角的![添加](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3112949161/p268631.png)图标，创建数据源。

        ![创建数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3112949161/p268637.png)

    3.  在新增Source页面，选择此前配置的PostgreSQL数据源，配置数据源信息。

        ![新增source](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3112949161/p268639.png)

        |参数|说明|
        |--|--|
        |名称|您可以输入数据源的名称。如Hologres。|
        |类型|选择`JDBC`。|
        |数据库|选择此前配置的数据源`postgresql`。|
        |用户名|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |密码|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |数据连接URL|数据连接URL，填写格式为`jdbc:postgresql://<host>:<port>/<databasename>`，其中各参数解释如下所示：        -   host：Hologres实例的公共或VPC网络地址，具体按部署环境决定。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取。
        -   port：端口，Hologres实例的端口。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取实例端口。
        -   databasename：数据库名称，Hologres创建的数据库名称。 |

    4.  单击**点击测试**按钮，如果页面提示**测试成功**，表示能够正常连接数据库。

    5.  单击**保存**，即完成Hologres数据连接的配置。

4.  数据分析

    您可以利用Hologres进行数据可视化操作，更多数据操作指导请参见[Davinci官方文档](https://edp963.github.io/davinci/docs/zh/2.1-source)


