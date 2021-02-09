---
keyword: [SET\_TABLE\_PROPERTY, 设置表属性, Hologres]
---

# SET\_TABLE\_PROPERTY

本文为您介绍在交互式分析Hologres中SET\_TABLE\_PROPERTY的用法。

## 函数概述

SET\_TABLE\_PROPERTY用于设置表的属性，包括索引、分布列、行存储、列存储以及生命周期等属性。如果您需要对创建的表进行修改、更新和删除，具体操作请参见[ALTER TABLE](/cn.zh-CN/Hologres SQL/DDL/TABLE/ALTER TABLE.md)或[DROP TABLE](/cn.zh-CN/Hologres SQL/DDL/TABLE/DROP TABLE.md)。

## 命令格式

```
CALL SET_TABLE_PROPERTY ( table_name, property, value )
WHERE PROPERTY IN
    orientation
  clustering_key
  segment_key
  bitmap_columns
  dictionary_encoding_columns
  time_to_live_in_seconds
  distribution_key
```

## 参数说明

SET\_TABLE\_PROPERTY参数说明如下表所示。

|参数|描述|
|--|--|
|table\_name|表名称。您也可以使用Schema限定表名称。表名称只能是小写英文字母`a~z`、大写英文字母`A~Z`、数字以及下划线`(_)`的组合，并且以字母开头。

如果表名称有特殊字符，则需要使用双引号`""`转义。由于系统对大小写不敏感，大写字母`A~Z`会被认为是小写字母`a~z`。 |
|property|属性名称。|
|orientation|用于指定数据库的表在Hologres中是列存储还是行存储。该参数仅支持和CREATE TABLE在同一事务中执行。 |
|clustering\_key|用于在指定的列建立聚簇索引。该参数仅支持和CREATE TABLE在同一事务中执行。 |
|segment\_key|用于指定某些列作为分段键，例如，指定时间列作为分段键。当查询条件包含分段列时，查询可以通过分段键快速找到相应数据对应的存储位置。

该参数仅支持和CREATE TABLE在同一事务中执行。 |
|bitmap\_columns|用于在指定列构建比特编码，快速过滤分段内部的数据。您可以单独使用该参数。|
|dictionary\_encoding\_columns|用于为指定列的值构建字典映射。字典编码可以转换字符串的比较为数字的比较，加速Group By、Filter等查询。您可以单独使用该参数。 |
|distribution\_key|用于指定数据库中表的分布策略。该参数仅支持和CREATE TABLE在同一事务中执行。 |
|time\_to\_live\_in\_seconds|表数据的生命周期，单位为秒。取值为非负数、整数或浮点数。您可以单独使用该参数。 |
|value|属性值。如果该参数包含列名，并且列名包含大写字母，则需要添加双引号`""`。|

## 使用示例

```
BEGIN;
CREATE TABLE ORDERS ( 
  O_ORDERKEY              INTEGER NOT NULL,
  O_CUSTKEY        INTEGER NOT NULL,
  O_ORDERSTATUS    TEXT NOT NULL,
  O_TOTALPRICE     DECIMAL(15,2) NOT NULL,
  O_ORDERDATE      DATE NOT NULL,
  O_ORDERPRIORITY  TEXT NOT NULL,  
  O_CLERK          TEXT NOT NULL, 
  O_SHIPPRIORITY   INTEGER NOT NULL,
  O_COMMENT        TEXT NOT NULL);
CALL SET_TABLE_PROPERTY ('ORDERS', 'clustering_key', 'O_ORDERKEY:asc,O_CUSTKEY:asc');
CALL SET_TABLE_PROPERTY ('ORDERS', 'segment_key', 'O_ORDERDATE');
CALL SET_TABLE_PROPERTY ('ORDERS', 'bitmap_columns', 'O_ORDERSTATUS,O_ORDERPRIORITY,O_CLERK,O_SHIPPRIORITY');
CALL SET_TABLE_PROPERTY ('ORDERS', 'dictionary_encoding_columns', 'O_ORDERSTATUS,O_ORDERPRIORITY,O_CLERK,O_SHIPPRIORITY');
CALL SET_TABLE_PROPERTY ('ORDERS', 'time_to_live_in_seconds', '172800');
COMMIT;
```

