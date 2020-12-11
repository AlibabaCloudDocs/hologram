---
keyword: [创建分区表, DDL, Hologres]
---

# CREATE PARTITION TABLE

CREATE PARTITION TABLE语句用于创建分区表。本文为您介绍CREATE PARTITION TABLE的用法。

## 使用限制

-   Hologres暂不支持插入数据至分区表父表，只支持插入数据至具体的分区表子表。

    **说明：** 实时计算Flink版支持实时写入数据至Hologres的分区表父表，详情请参见[t1997136.dita\#task\_1997136/section\_7av\_gr2\_kws](t1997136.dita#task_1997136/section_7av_gr2_kws)。

-   只有TEXT、VARCHAR以及INT类型的数据才可以作为分区键（Partition Key）。
-   一个分区只能创建一张分区表。
-   `PARTITION BY`仅支持`LIST`分区，切分`PARTITION BY LIST`只能取唯一值。
-   分区表必须和父表在同一个Schema。

## 语法

创建分区表的语法如下。

```
//创建分区表父表。
CREATE TABLE [IF NOT EXISTS] [schema_name.]table_name PARTITION BY list (column_name) ([
  {
   column_name column_type [column_constraints, [...]]
   | table_constraints
   [, ...]
  }
]);

//创建分区表子表。
CREATE TABLE [IF NOT EXISTS] [schema_name.]table_name PARTITION OF <parent_table>
    FOR VALUES IN (string_literal);
```

## 示例

创建分区表的示例SQL语句如下。

```
BEGIN;
CREATE TABLE hologres_parent(a text primary key,
 b int NOT NULL , 
 c TIMESTAMPTZ NOT NULL , 
 d text)
 PARTITION BY list(a);
call set_table_property('hologres_parent', 'orientation', 'column');
call set_table_property('hologres_parent', 'clustering_key', 'a,b'); 
call set_table_property('hologres_parent', 'segment_key', 'c');
call set_table_property('hologres_parent', 'bitmap_columns', 'a,d'); 
call set_table_property('hologres_parent', 'dictionary_encoding_columns', 'a,d'); 
call set_table_property('hologres_parent', 'time_to_live_in_seconds', '86400');

CREATE TABLE hologres_child2 PARTITION of hologres_parent FOR VALUES IN('b');
CREATE TABLE hologres_child3 PARTITION of hologres_parent FOR VALUES IN('c');
CREATE TABLE hologres_child1 PARTITION of hologres_parent FOR VALUES IN('a');
COMMIT;
```

