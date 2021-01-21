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
-   若是表有主键，分区键必须是主键的一个子集。

## 语法

创建分区表的语法如下。

```
//典型的 create partition table 语句
create table [if not exists] [schema_name.]table_name partition by list (column_name) ([
  {
   column_name column_type [column_constraints, [...]]
   | table_constraints
   [, ...]
  }
]);

//典型的 create child partition table 语句
create table [if not exists] [schema_name.]table_name partition of parent_table
  for values in (string_literal);
```

## 示例

创建分区表的示例SQL语句如下。

```
//不带主键的分区表
begin;
create table hologres_parent(
  a text, 
  b int, 
  c timestamp, 
  d text
) 
  partition by list(a);
call set_table_property('hologres_parent', 'orientation', 'column');
create table hologres_child1 partition of hologres_parent for values in('v1');
create table hologres_child2 partition of hologres_parent for values in('v2');
create table hologres_child3 partition of hologres_parent for values in('v3');
commit;

//带主键的分区表
begin;
create table hologres_parent_2(
  a text , 
  b int, 
  c timestamp, 
  d text,
  primary key(a,b)
  )
  partition by list(a);
call set_table_property('hologres_parent_2', 'orientation', 'column');
create table hologres_child_1 partition of hologres_parent_2 for values in('v1');
create table hologres_child_2 partition of hologres_parent_2 for values in('v2');
create table hologres_child_3 partition of hologres_parent_2 for values in('v3');
commit;
```

