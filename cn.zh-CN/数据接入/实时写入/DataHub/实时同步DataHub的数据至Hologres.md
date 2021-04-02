---
keyword: [同步数据, DataHub, Hologres]
---

# 实时同步DataHub的数据至Hologres

本文为您介绍如何使用Connector同步DataHub中的数据至Hologres。

-   开通Hologres并连接开发工具，详情请参见[psql快速入门](/cn.zh-CN/快速入门/psql快速入门.md)。
-   开通DataHub，详情请参见[快速入门](https://help.aliyun.com/document_detail/206546.html)。
-   DataHub与Hologres的概念映射如下表所示。

    |DataHub|Hologres|
    |-------|--------|
    |Project|Database|
    |Topic|Table|


数据总线（DataHub）支持发布、订阅和分发流式数据 ，帮助您轻松构建基于流式数据的业务应用。

Hologers是实时交互式分析产品，兼容PostgreSQL协议，与大数据生态无缝打通，支持高并发和低延时地分析处理万亿级数据。

您可以使用DataHub的数据同步功能，通过Connector实时同步DataHub中的数据至Hologres。方便您在Hologres中多维分析和实时处理数据。

## 操作步骤

1.  DataHub准备数据源。

    在DataHub中准备数据源的步骤如下：

    1.  创建项目。

        1.  登录[DataHub控制台](https://dhsnext.console.aliyun.com/cn-hangzhou/projects?spm=5176.cndatahub.0.0.677af05eXclMic)，单击左侧导航栏的**项目管理**。
        2.  在**项目列表**页面单击**新建项目**。
        3.  配置**新建项目**弹框的参数，单击**创建**。
        ![a](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2399209951/p129088.png)

    2.  新建Topic。

        1.  成功创建项目后，在**项目列表**页面单击项目名称，进入项目详情页。
        2.  单击项目详情页右上角的**新建Topic**，配置**新建Topic**弹框的参数。
        ![吧](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2399209951/p129089.png)

        **说明：** 目前仅支持同步DataHub的TUPLE数据至Hologres中。

        创建完成的Topic示例如下。

        |索引|名称|类型|是否允许为NULL|
        |--|--|--|---------|
        |0|l\_orderkey|BIGINT|False|
        |1|l\_partkey|BIGINT|False|
        |2|l\_suppkey|BIGINT|False|
        |3|l\_linenumber|BIGINT|False|
        |4|l\_quantity|DECIMAL|True|
        |5|l\_extendedprice|DECIMAL|True|
        |6|l\_discount|DECIMAL|True|
        |7|l\_tax|DECIMAL|True|
        |8|l\_returnflag|STRING|True|
        |9|l\_linestatus|STRING|True|
        |10|l\_shipdate|TIMESTAMP|True|
        |11|l\_commitdate|TIMESTAMP|True|
        |12|l\_receiptdate|TIMESTAMP|True|
        |13|l\_shipinstruct|STRING|True|
        |14|l\_shipmode|STRING|True|
        |15|l\_comment|STRING|True|

    3.  写入数据。

        成功创建Topic后，您需要使用工具（例如Blink）或者程序写入数据至Topic中。使用工具写入数据的结果示例如下。

        ![写入数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2399209951/p129091.png)

2.  Hologres创建数据接收表。

    在Hologres中创建一张用于接收数据的表，表的字段类型与DataHub中Topic的字段类型相互映射。示例建表语句如下。

    ```
    BEGIN;
    CREATE TABLE lineitem ( 
    L_ORDERKEY BIGINT NOT NULL,
    L_PARTKEY BIGINT NOT NULL,
    L_SUPPKEY BIGINT NOT NULL,
    L_LINENUMBER BIGINT NOT NULL,
    L_QUANTITY DECIMAL(20,10),
    L_EXTENDEDPRICE DECIMAL(20,10),
    L_DISCOUNT DECIMAL(20,10),
    L_TAX DECIMAL(20,10),
    L_RETURNFLAG TEXT,
    L_LINESTATUS TEXT,
    L_SHIPDATE TIMESTAMPTZ,
    L_COMMITDATE TIMESTAMPTZ,
    L_RECEIPTDATE TIMESTAMPTZ,
    L_SHIPINSTRUCT TEXT,
    L_SHIPMODE TEXT,
    L_COMMENT TEXT
    );
    
    CALL set_table_property('lineitem', 'orientation', 'column');
    CALL set_table_property('lineitem', 'shard_count', '8');
    
    COMMIT;
    ```

3.  创建Hologres Connector。

    1.  单击DataHub中已创建的Topic，进入Topic详情页。

    2.  单击Topic详情页右上角的**+同步**。

    3.  在**新建Connector**界面单击**Hologres**，配置**新建Connector**弹框的参数，单击**创建**。

        ![connector](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3399209951/p129426.png)

        |参数|描述|说明|
        |--|--|--|
        |Instance|Hologres实例的ID。|进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的，获取**实例ID**。|
        |Project|Hologres的数据库名称。|无|
        |Topic|Hologers用于接收数据的表名称。|无|
        |导入字段|需要导入Hologres的字段。|可以根据实际业务需求选择导入部分或全部字段。|
        |鉴权模式|默认为AK。|无|
        |AccessId|访问Hologres实例的AccessKey ID。|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取用户的AccessKey ID。|
        |AccessKey|访问Hologres实例的AccessKey Secret。|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。|

4.  同步DataHub的数据至Hologres。

    成功创建Connector后，您可以在Topic详情页的**数据同步**中查看实时同步数据的状态。

    ![chakan](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3399209951/p129356.png)

5.  Hologres查询数据。

    您可以连接Hologres实例至开发工具，实时查询同步至Hologres中的数据，详情请参见[概述](/cn.zh-CN/连接开发工具/概述.md)。示例查询语句如下。

    ```
    SELECT COUNT(*) FROM lineitem;
    ```


## 数据类型映射

DataHub与Hologres的数据类型映射如下表所示。

|DataHub|Hologres|
|-------|--------|
|BIGINT|BIGINT|
|STRING|TEXT|
|BOOLEAN|BOOLEAN|
|DOUBLE|DOUBLE PRECISION|
|TIMESTAMP|TIMESTAMPTZ|
|DECIMAL|DECIMAL|

