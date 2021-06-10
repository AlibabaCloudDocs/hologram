---
keyword: [MaxCompute加速, Hologres, 快速查询MaxCompute数据]
---

# 通过创建外部表加速查询MaxCompute数据

本文为您介绍在Hologres中如何通过创建外部表的方式，实现MaxCompute加速查询，帮助您快速查看MaxCompute的数据。

大数据计算服务（MaxCompute，原名ODPS）是一种快速、完全托管的EB级数据仓库，致力于批量结构化数据的存储和计算，提供海量数据仓库的解决方案及分析建模服务。

Hologres是兼容PostgreSQL协议的实时交互式分析数据仓库，在底层与MaxCompute无缝连接，支持您使用创建外部表的方式实现MaxCompute加速查询，无冗余存储，无需导入导出数据，即可快速获取查询结果。

您也可以导入数据至Hologres后，再进行查询。相比其他非大数据生态产品，Hologres导入导出数据的速度性能更佳。

您可以根据业务特性和场景，选择查询方式：

-   在Hologres中直接查询MaxCompute的数据。

    该方式适用于查询数据量小于200GB的场景。

    **说明：** 数据量小于200GB指经过分区过滤后，命中分区的数据量大小，与查询相关字段的大小无关。

-   导入MaxCompute的数据至Hologres后再进行查询。

    该方式适用于单张表数据量大于200GB的查询、复杂查询、包含索引的查询和涉及UPDATE及INSERT等操作的场景。


## 注意事项

通过创建外部表加速查询MaxCompute数据时，您需要注意如下内容：

-   Hologres只能加速查询MaxCompute的内部表，不能查询MaxCompute的外部表和VIEW。
-   MaxCompute的表数据更新之后，在Hologres存在缓存（一般为5分钟内）才能加速更新后的数据，如果您需要实时查询更新后的数据，可以使用[IMPORT FOREIGN SCHEMA](/intl.zh-CN/SQL参考/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md)语法更新外部表，就能实时查询更新后的数据。
-   MaxCompute的Schema更新之后，Hologres不会自动更新，需要手动更新。
-   MaxCompute的分区与Hologres无强映射关系，映射至hologres之后均为普通字段。
-   MaxCompute与Hologres数据类型一一映射，建表时您可以查看映射关系，详情请参见[数据类型汇总](/intl.zh-CN/SQL参考/数据类型/数据类型汇总.md)。

## 查询MaxCompute非分区表数据

1.  准备MaxCompute非分区表数据。

    创建MaxCompute非分区表并导入数据，详情请参见[创建表](/intl.zh-CN/快速入门/通过MaxCompute客户端使用MaxCompute/创建和查看表.md)。您也可以选择已创建的MaxCompute非分区表。

    本实验选用已创建的MaxCompute表，示例SQL语句如下。

    ```
    CREATE TABLE weather (
        city            STRING ,
        temp_lo         int,           --最低温度
        temp_hi         int           --最高温度
    );
    INSERT INTO weather VALUES 
    ('beijing',40,50),
    ('hangzhou',46,55);
    ```

2.  Hologres创建外部表。

    在Hologres中创建一张用于映射MaxCompute数据的外部表。您可以选择查询部分字段或全部字段。示例语句如下。

    ```
    CREATE FOREIGN TABLE weather1 (
     city text,
     temp_lo int8,
     temp_hi int8
    )
    SERVER odps_server
    OPTIONS (project_name '<projectname>',table_name 'weather');
    ```

    参数说明如下表所示。

    |参数|描述|
    |--|--|
    |SERVER|外部表服务器。您可以直接调用Hologres底层已创建的名为**odps\_server**的外部表服务器。详细原理请参见[Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k)。 |
    |project\_name|MaxCompute表所在的项目名称。|
    |table\_name|需要查询的MaxCompute表名称。|

    **说明：** ：

    -   Hologres的字段类型必须与MaxCompute的字段类型保持一致，数据类型的映射关系请参见[MaxCompute与Hologres的数据类型映射](/intl.zh-CN/SQL参考/数据类型/数据类型汇总.mdsection_w14_cec_th7)。
    -   Hologres支持使用`IMPORT FOREIGN SCHEMA`语句批量创建外部表，详情请参见[IMPORT FOREIGN SCHEMA](/intl.zh-CN/SQL参考/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md)。
    -   Hologres仅支持加速查询MaxCompute的内部表数据，不支持加速查询MaxCompute的外部表和视图。
3.  查询外部表数据。

    成功创建外部表后，您可以直接查询外部表，即可查询到MaxCompute的数据。示例语句如下。

    ```
    SELECT * FROM weather1;
    ```


## 查询MaxCompute分区表数据

1.  准备Maxcompute分区表数据。

    创建一张MaxCompute分区表并导入数据，详情请参见[分区和列操作](/intl.zh-CN/开发/SQL及函数/DDL语句/分区和列操作.md)。您也可以选择已创建的MaxCompute分区表。

    本实验选用数据地图已创建的分区表，示例语句如下。

    ```
    create table odps_test
    (
        shop_name     string,
        customer_id   string,
        total_price   INT 
    )
    partitioned by (sale_date string);
    ```

2.  Hologres创建外部表。

    在Hologres中创建一张用于映射MaxCompute源数据表的外部表。示例语句如下。

    ```
    CREATE FOREIGN TABLE table_odps (
     shop_name text,
     customer_id text,
     total_price int8,
     sale_date text
    )
    SERVER odps_server
    OPTIONS (project_name '<projectname>', table_name 'odps_test');
    ```

    **说明：** Hologres的外部表仅用于字段映射，不存储数据。MaxCompute中的分区字段映射为Hologres的普通字段。

3.  查询分区表数据。

    查询整张表数据，示例SQL语句如下。

    ```
    SELECT * FROM table_odps;
    ```

    查询分区表数据，示例SQL语句如下。

    ```
    SELECT * FROM table_odps 
    WHERE sale_date = '2013';
    ```


## 批量创建外部表

如果您需要加速查询大批量的MaxCompute表，可以通过批量创建外部表的方式来实现。在Hologres您可以使用SQL语句或者管理控制台可视化的方式批量创建外表。

-   Hologres支持使用`IMPORT FOREIGN SCHEMA`语句批量创建外部表，详情请参见[IMPORT FOREIGN SCHEMA](/intl.zh-CN/SQL参考/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md)。
-   通过HoloWeb批量创建外部表，详请请参见[批量创建外部表](/intl.zh-CN/连接开发工具/HoloWeb/连接管理/MaxCompute加速/批量创建外部表.md)。

## 外部表查询性能优化

当外部表查询性能不满足当前查询时，您可以通过MaxCompute合并小文件，优化Hologres SQL等标准手段进行优化，以提升查询性能。从Hologres V0.10版本开始，Hologres采用全新外部表加速引擎，相比低于V0.10版本实例，查询MaxCompute表性能提升30%~100%左右。详情请参见[优化MaxCompute外部表的查询性能](/intl.zh-CN/最佳实践/性能调优/优化MaxCompute外部表的查询性能.md)。

