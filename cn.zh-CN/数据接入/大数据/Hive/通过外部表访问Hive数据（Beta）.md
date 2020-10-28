---
keyword: [Hologres, 接入Hive数据]
---

# 通过外部表访问Hive数据（Beta）

本文为您介绍Hologres如何使用Hive Access服务访问Hive数据。

Hologres 0.8及之后版本，支持以Apache Hive表的形式访问存储在Hadoop中的信息。该功能由Hive Access服务提供，用于访问Hive的元数据和其他数据。

Hive Access服务以分布式方式部署在Hologres实例中，所有Hive Access服务均可以提供元数据访问。读取数据时，Hologres并行向一个或多个Hive Access服务发出表扫描请求，Hive Access服务直接从HDFS或其他支持Hive的表中读取Hive数据。

**说明：** 当前Hive Data Access服务及Hologres对接Hive还属于Beta版本，不能保证生产的稳定性，不建议直接用于生产服务。Hive Data Access正在快速发展之中，您在使用过程中遇到问题，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)反馈。

## 使用限制

-   Hologres0.8版本中的Hive Access服务暂未投入生产，不建议您在生产中使用。
-   Hologres仅提供对Hive数据的读取访问权限。
-   Hologres并非支持所有Hive数据类型。例如，Hive Access当前不支持Hive中的复杂数据类型。
-   不强制执行安全性。因此，它可能无法在安全的Hive或Hadoop环境中工作。
-   仅测试文本，SqueenceFile，Parquet和ORC格式。其他文件格式尚未经过测试或验证。

## 支持的版本

Hive Access服务是使用Apache Hive 3.1.2版本和Apache Hadoop 2.8.2版本构建的。当前仅测试并验证了发布的Hive Access可以运行Apache Hadoop 2.7.2，但未测试及验证其他Hive和Hadoop的版本。如果您不确认您的Hive或Hadoop版本可以正常工作，请采用我们所验证的版本进行作业。

## 操作步骤

Hologres使用PostgreSQL的FDW（外部数据包装器）机制读取Hive的元数据和其他数据，详情请参见[Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html)。

Hologres 0.8版本内置了Hive FDW，您可以通过ive Access服务调用Hive FDW，直接读取Hive中的数据，步骤如下：

1.  配置单元访问服务。

    您需要在Hive部署中配置Hive Access服务。在hive-access-site.xml中的`/ var / hologres / hive_access`下添加如下配置。

    ```
    <property>
          <name>hive-access.hive.conf.dir</name>
          <value>/path/to/your/hive conf dir</value>
            <description>The full path to the direction of your hive conf dir, where hive-site.xml can be found.</description>
      </property>
    ```

2.  准备Hive数据源表。

    本文档使用TPCH数据集为例进行实验。数据源表如下。

    **说明：** 创建数据集，详情请参见[github库](https://github.com/hortonworks/hive-testbench)。本实验中，假定您的Hive中已经存在想要查询的数据。

    ```
    hive> show databases;
    OK
    default
    tpch
    hive> use tpch;
    OK
    hive> show tables;
    OK
    customer
    lineitem
    nation
    orders
    part
    partsupp
    region
    supplier
    ```

3.  创建Hive FDW。

    在Hologres中创建外部服务器FDW，语句如下。

    ```
    create extension hive_fdw;  //创建FDW。
    create server hive_server FOREIGN DATA WRAPPER hive_fdw;  //创建服务器。
    ```

    参数说明如下表所示。

    |参数|描述|
    |--|--|
    |hive\_fdw|默认为hive\_fdw，您可以直接调用。|
    |hive\_server|创建的Hive服务器名称，自定义取值。|

4.  Hologres创建外部表。

    创建FDW后，您可以在Hologres中创建用于查询Hive数据的外部表。外部表的字段以及字段类型需要与Hive表保持一致。示例创建外部表的语句如下。

    **说明：** 外部表仅做字段映射，不存储数据，数据仍然存储在Hive表中。

    ```
    create foreign table if not exists orders(
        O_ORDERKEY      INT not null,
        O_CUSTKEY       INT not null,
        O_ORDERSTATUS   TEXT        ,
        O_TOTALPRICE    DECIMAL(15,2) ,
        O_ORDERDATE     TEXT,
        O_ORDERPRIORITY TEXT        ,
        O_CLERK         TEXT        ,
        O_SHIPPRIORITY  INT         ,
        O_COMMENT       TEXT
    ) server hive_server options (schema_name 'tpch', table_name 'orders');   
    ```

    参数说明如下表所示。

    |参数|描述|示例|
    |--|--|--|
    |schema\_name|Hive中的数据库名称。|tpch|
    |table\_name|Hive中的表名称。|orders|

    您也可以使用[import foreign table](https://help.aliyun.com/document_detail/158715.html?spm=a2c4g.11186623.6.635.101f760ent1AIp)批量创建外部表。

5.  查询Hive表数据。

    在Hologres创建Hive映射的外部表后，您可以根据业务需求查询相关数据。您可以选择查询Hive表的内容，也可以将Hive表与Hologres中的其他表相关联后，再进行查询。示例语句如下。

    ```
    select count(*) from orders where o_orderdate > '1998-07-01';
    
      count  
    ---------
     1994599
    (1 row)
    ```


## 数据类型映射

Hive与Hologres数据类型映射如下表所示。

|Hive数据类型|Hologres数据类型|是否支持|
|--------|------------|----|
|tinyint|smallint|支持|
|smallint|smallint|支持|
|int|integer|支持|
|bigint|bigint|支持|
|real|float|支持|
|double|double precision|支持|
|decimal\(p, s\)|numeric\(p, s\)|支持|
|boolean|boolean|支持|
|char\(n\)|char\(n\)|支持|
|varchar\(n\)|varchar\(n\)|支持|
|string|text|支持|
|date|date|支持|
|timestamp|timestamp without time zone|支持|
|array|array|支持|
|binary|bytea|不支持|

