# Dataiku

本文为您介绍Dataiku如何连接Hologres并可视化分析数据。

## 使用限制

由于Dataiku的限制，支持直接使用SQL Notebook查询外部表，但不支持使用Import tables to datasets查看外部表。

## 操作步骤

1.  安装Dataiku

    安装Dataiku，详请参见[Dataiku官方文档](https://www.dataiku.com/product/get-started/)。

2.  连接Hologres

    1.  登录Dataiku，单击**Blank project**创建一个项目，本示例的项目命名为Hologres。

        ![创建项目](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8221756161/p254370.png)

    2.  在项目详情页面，单击+IMPORT YOUR FIRST DATASET进入数据源页面。

        ![导入数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8221756161/p254372.png)

    3.  在数据源页面，选择**SQL**数据源类型下的**PostgreSQL**。在页面单击**create a new connection**。

        ![创建连接](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8221756161/p254412.png)

    4.  在页面上方单击**+NEW CONNECTION**，从下拉框中选择**PostgreSQL**，创建新的连接。

        ![创建连接](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8221756161/p254414.png)

    5.  根据需求填写BASIC PARAMS，如有需要您也可以填写schema参数。

        ![参数配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8221756161/p254432.png)

        具体参数配置如下所示：

        |参数|描述|
        |--|--|
        |New connection name|输入您的连接名称。|
        |Host|Hologres实例的公共网络地址。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取网络地址。|
        |Port|Hologres的实例端口。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取实例端口。|
        |Database|Hologres的数据库名称。|
        |User|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |Password|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |更多参数|其余更多参数说明，请参见[Dataiku官方文档](https://doc.dataiku.com/dss/latest/connecting/sql.html)。|

    6.  参数填写完成后，单击页面下方的**TEST**，如果提示可以成功连接，您就可以单击**CREATE**创建连接。

        ![连接成功](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8221756161/p254439.png)

3.  数据分析

    完成配置之后，您可以单击页面底部的**Import tables to datasets**批量导入数据库中的表结构到数据集。导入之后您可以进行数据分析，更多关于数据分析的操作请参见[Dataiku官方文档](https://doc.dataiku.com/dss/latest/connecting/sql.html)。

    ![分析](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8221756161/p254443.png)


