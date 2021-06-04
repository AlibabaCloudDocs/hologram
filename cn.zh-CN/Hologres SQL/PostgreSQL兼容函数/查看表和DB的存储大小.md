---
keyword: [Hologres, 查看存储大小]
---

# 查看表和DB的存储大小

Hologres兼容PostgreSQL，当前支持查看表或者DB的存储大小。本文将会为您介绍如何使用SQL语句查看表和DB的存储大小。

## 注意事项

在Hologres中查看表和DB的存储大小需要注意如下事项：

-   在查看表和DB的存储规格之前，您需要开通Hologres实例并连接开发工具，操作示例请参见[HoloWeb快速入门](/cn.zh-CN/快速入门/HoloWeb快速入门.md)。
-   仅Hologres V0.9及以上版本支持查看对象的存储大小，如果您的实例是V0.9以下版本，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)升级实例。

## 查看表的存储大小

-   使用说明

    当前仅支持查看内部表的存储规格大小，不支持直接查看分区父表（查看分区父表返回结果是0），需要指定分区子表进行查看。如果查询到表的规格大小是0，则返回空值。

-   函数语法

    ```
    select pg_relation_size('table_name');--返回单位是Byte
    ```

-   参数说明

    |参数|说明|
    |--|--|
    |table\_name|表示待查询的当前数据库下的表名称。|

-   返回值：返回值的单位是 Byte，类型为字符串。返回的数据为该表此刻的内存所占空间和物理磁盘空间。

    如果您需要提高可读性，可以使用pg\_size\_pretty函数进行查询，具体语法如下：

    ```
    --查看单表存储
    SELECT pg_size_pretty(pg_relation_size('table_name'));
    
    --查看所有表大小
    SELECT table_schema || '.' || table_name AS table_full_name, 
    pg_size_pretty(pg_relation_size('"' || table_schema || '"."' || table_name || '"')) AS table_size,
    pg_relation_size('"' || table_schema || '"."' || table_name || '"') AS  order_size
    FROM information_schema.tables
    WHERE table_schema NOT IN ('pg_catalog','information_schema','hologres')
    ORDER BY order_size DESC;
    ```


## 查看schema存储大小

-   使用说明

    您可以通过执行SQL语句查看对应schema下面的所有表的大小。

-   函数语法

    ```
    SELECT table_schema, pg_size_pretty(SUM(pg_relation_size( table_schema  || '.' || table_name)::decimal)) AS schema_size
    FROM information_schema.tables 
    WHERE table_schema = 'schema_name'
    GROUP BY table_schema;
    ```

-   参数说明

    |参数|说明|
    |--|--|
    |schema\_name|表示当前表所对应的schema名称。|

-   返回值：返回值的单位是 Byte。

## 查看DB的存储大小

-   使用说明

    仅支持查看当前连接DB和该DB下内部表的存储规格大小。

-   函数语法

    ```
    select pg_database_size(current_database()); --返回单位是Byte
    ```

-   参数说明

    |参数|说明|
    |--|--|
    |current\_database|指代当前DB。您无需替换参数，直接执行函数命令语句即可查询当前DB的存储规格大小。|

-   返回值：返回值的单位是 Byte。返回的数据为指定DB下面所有Hologres表的大小和DB下面产生的WAL（Write-Ahead Log）日志大小。

    如果您需要提高可读性，可以使用pg\_size\_pretty函数进行查询，具体语法如下：

    ```
    select pg_size_pretty(pg_database_size(current_database())); --返回单位是KB或者MB等单位
    ```


