---
keyword: [Metabase, Hologres, 可视化分析]
---

# Metabase

Metabase是一个开源的商业智能工具，您可以通过它理解数据、分析数据，进行数据查询并获取格式化结果（图形化视图），以数据驱动决策。Hologres兼容PostgreSQL，支持直接连接Metabase进行数据分析。本文为您介绍Metabase如何连接Hologres并可视化分析数据。

## 操作步骤

1.  安装Metabase

    安装Metabase，详请参见[Metabase官方文档](https://www.metabase.com/docs/latest/operations-guide/installing-metabase.html)。

2.  连接Hologres

    1.  登录Metabase，在顶部导航栏的右上角单击![设置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1706946161/p254220.png)图标，在下拉菜单中单击**管理员**。

        ![管理员](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1706946161/p254226.png)

    2.  单击Metabase管理员页面顶部的数据库。在数据库页面右上角，单击**添加数据库**，增加Hologres的连接。

        ![添加数据库](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1706946161/p254229.png)

    3.  选择数据库类型并配置连接数据库参数后，单击**保存**。

        ![参数配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1706946161/p254238.png)

        具体参数配置如下所示：

        |参数|描述|
        |--|--|
        |数据库类型|在下拉列表中选择**PostgreSQL**。|
        |主机|Hologres实例的公共网络地址。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取网络地址。|
        |端口|Hologres的实例端口。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取实例端口。|
        |数据库名称|Hologres的数据库名称。|
        |用户名|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |密码|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |更多参数|其余更多参数说明请参见[Metabase官方文档](https://www.metabase.com/docs/latest/administration-guide/01-managing-databases.html)。|

3.  数据分析

    完成配置之后，您即可开始连接Hologres进行数据分析，更多关于数据分析的操作请参见[Metabase官方文档](https://www.metabase.com/docs/latest/)。

    ![数据分析](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1706946161/p254241.png)


