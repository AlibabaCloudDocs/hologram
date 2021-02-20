---
keyword: [SQL, Hologres, 分区表, 非分区表]
---

# 使用SQL导入MaxCompute的数据至Hologres

本文为您介绍如何使用SQL语句导入MaxCompute的数据至Hologres。

针对MaxCompute的业务数据大于100 GB，查询复杂度高且要求查询响应达到秒级的场景，Hologres支持直接导入数据并查询。

## 导入MaxCompute的非分区表数据至Hologres并查询

1.  准备MaxCompute的非分区表数据。

    在MaxCompute中创建一张非分区源数据表，或直接选用中已创建的非分区表。

    示例选取数据地图已创建的非分区表**odps\_test1**，SQL语句如下。

    ```
    CREATE TABLE odps_test1(
        id int,
        name STRING ,
        class STRING 
    );
    INSERT INTO odps_test1 VALUES 
    (1,'Tom','class3'),
    (2,'Neal','class4'),
    (3,'Tony','class4'),
    (4,'David','class5');
    ```

2.  创建外部表。

    在Hologres中创建一张用于映射MaxCompute源数据表的外部表。示例SQL语句如下。

    ```
    CREATE FOREIGN TABLE holo_test1 (
     id int8,
     name text,
     class text
    )
    SERVER odps_server
    OPTIONS (project_name '<odpsprojectname>', table_name 'odps_test1');
    ```

    |参数|描述|
    |--|--|
    |Server|您可以直接调用Hologres底层已创建的名为**odps\_server**的外部表服务器。详细原理请参见[Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k)。|
    |Project\_Name|MaxCompute表所在的项目名称。|
    |Table\_Name|需要查询的MaxCompute表名称。|

    外部表字段的数据类型与MaxCompute表字段的数据类型保持一致，数据类型的映射关系请参见[MaxCompute与Hologres的数据类型映射](/cn.zh-CN/Hologres SQL/数据类型/数据类型汇总.md)。

3.  创建存储表。

    在Hologres中创建一张用于接收MaxCompute源表数据的存储表。

    存储表的字段类型与MaxCompute表的字段类型保持一致。

    示例SQL语句如下。

    ```
    CREATE TABLE holo_test2 (
     id int8,
     name text,
     class text
    );
    ```

4.  导入数据至Hologres。

    使用`INSERT`语句导入MaxCompute的数据至Hologres，示例如下。

    ```
    INSERT INTO holo_test2
    SELECT 
    id as id,
    name as name,
    class as class
    FROM holo_test1;
    ```

5.  查询表数据。

    在Hologres中查询导入的MaxCompute表数据。示例SQL语句如下。

    ```
    SELECT * FROM holo_test2;
    ```


## 导入MaxCompute的分区表数据至Hologres并查询

1.  准备MaxCompute的分区表数据。

    在MaxCompute中创建一张分区数据表，详情请参见[分区和列操作](/cn.zh-CN/开发/SQL及函数/DDL语句/分区和列操作.md)。您也可以选取已创建的分区表。

    示例选取数据地图中已创建的分区表**odps\_test2**，SQL语句如下。

    ```
     CREATE TABLE odps_test2
    (
        shop_name     string,
        customer_id   string,
        total_price   INT 
    )
    PARTITIONED BY (sale_date string);
    INSERT  overwrite table odps_test2 partition (sale_date='2013')values ('shop', '1234', 12);
    INSERT  overwrite table odps_test2 partition (sale_date='2014')values ('rest', '1111', 13);
    INSERT  overwrite table odps_test2 partition (sale_date='2015')values ('texy', '2222', 14);
    ```

2.  创建外部表。

    在Hologres中创建一张用于映射MaxCompute源数据表的外部表。示例SQL语句如下。

    ```
    CREATE FOREIGN TABLE table1_odps (
     shop_name text,
     customer_id text,
     total_price int8,
     sale_date text
    )
    SERVER odps_server
    OPTIONS (project_name '<odpsprojectname>', table_name 'odps_test2');
    ```

3.  创建存储表。

    在Hologres中创建一张用于接收MaxCompute源表数据的存储表。

    MaxCompute的分区与Hologres的分区无强映射关系。您可以导入MaxCompute的分区表数据至Hologres的分区表或非分区表。

    本次实验导入MaxCompute的分区表数据至Hologres的分区表中。示例SQL语句如下。

    ```
    CREATE TABLE table1_holo (
     shop_name text,
     customer_id text,
     total_price int8,
     sale_date text
    )
    PARTITION BY LIST (sale_date);
    ```

    **说明：** ：Hologres不支持多级分区。MaxCompute的多级分区表数据可以导入至Hologres的非分区表或者一级分区表。

4.  创建子分区。

    在Hologres中创建一张用于接收分区数据的子分区表。示例SQL语句如下。

    ```
    CREATE TABLE  table1_holo_0001
    PARTITION  of table1_holo
    for VALUES  in ('2015');
    ```

5.  导入数据至Hologres。

    使用`INSERT`语句导入MaxCompute的数据至Hologres的子分区表中，示例如下。

    ```
    INSERT INTO table1_holo_0001
    SELECT
     shop_name as shop_name,
     customer_id as customer_id,
     total_price as total_price,
     sale_date as sale_date
     from table1_odps
     WHERE sale_date = '2015';
    ```

6.  查询表数据。

    在Hologres中查询导入的MaxCompute表数据。示例SQL语句如下。

    ```
    SELECT * FROM table1_holo;
    ```


## 通过可视化工具或周期性调度同步数据

如果您需要一次性同步大数据量，可以通过可视化工具或者调度任务来实现。

-   Holoweb一键同步表数据：通过Holoweb可以实现一键同步MaxCompute数据，无需SQL语句就能实现数据同步，但是Holoweb无法配置调度任务。更多关于Holoweb的操作，请参见[一键同步MaxCompute数据](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/MaxCompute加速/一键同步MaxCompute数据.md)。
-   DataWorks调度任务同步：通过DataWorks实现周期性的MaxCompute数据导入，请参见[Hologres开发：周期性调度](/cn.zh-CN/连接开发工具/DataWorks数仓开发/数据开发/Hologres开发：周期性调度.md)。

