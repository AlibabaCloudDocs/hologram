---
keyword: [创建分区表, DDL, Hologres]
---

# CREATE PARTITION TABLE

CREATE PARTITION TABLE语句用于创建分区表。本文为您介绍CREATE PARTITION TABLE的用法。

## 命令说明

Partition Table，也叫分区表。父表按分区键（Partition Key）的值划分为不同的子表，子表对外可见。下文无特殊说明的父表和子表皆指分区父表和子表。

分区表在使用时，需要提前创建子表。`create partition table`命令被用于创建分区表。

分区表的不同分区子表采用不同的文件存储，查询时带上分区条件，指定所需查询的分区，避免全表扫描，快速定位存储文件，提高处理效率。通常将事实表按照日期划分为不同的分区表。

## 使用限制

-   Hologres暂不支持插入数据至分区表父表，只支持插入数据至具体的分区表子表。

    **说明：** 实时计算Flink版支持实时写入数据至Hologres的分区表父表，详情请参见[t1997136.dita\#task\_1997136/section\_7av\_gr2\_kws](t1997136.dita#task_1997136/section_7av_gr2_kws)。

-   只有TEXT、VARCHAR以及INT类型的数据才可以作为分区键（Partition Key）。
-   一个分区规则只能创建一个分区表。
-   `PARTITION BY`类型仅支持`LIST`分区，切分`PARTITION BY LIST`只能取唯一值。
-   分区父表和子表必须要在同一个Schema。
-   若是表有主键，分区键必须是主键的一个子集。

## 命令格式

创建分区表的命令格式如下。

```
--创建分区父表语句
create table [if not exists] [schema_name.]table_name partition by list (column_name) ([
  {
   column_name column_type [column_constraints, [...]]
   | table_constraints
   [, ...]
  }
]);

--创建分区子表语句
create table [if not exists] [schema_name.]table_name partition of parent_table
  for values in (string_literal);
```

## 参数说明

创建分区表的参数说明如下。

|参数|说明|
|--|--|
|if not exists|如果已经存在相同名称的表，不会抛出一个错误，而会发出一个通知，告知表关系已存在。|
|schema\_name|表所在的schema名称，若是在同一个schema创建父表和子表，可以不需要指定schema名称。若是需要跨schema创建父表和子表，需要指定schema名称。|
|table\_name|需要创建的分区父表或分区子表的名称。|
|column\_name|新表中要创建的字段名。|
|column\_type|字段的数据类型。|
|column\_constraints|列约束的名称。|
|table\_constraints|表约束的名称。|
|parent\_table|子表对应的父表名称。|
|string\_literal|分区键。|

## 使用示例

创建分区表的SQL语句示例如下。

-   示例1：在public schema下创建不带主键的分区父表和对应的分区子表。

    ```
    begin;
    create table public.hologres_parent(
      a text, 
      b int, 
      c timestamp, 
      d text
    ) 
      partition by list(a);
    call set_table_property('public.hologres_parent', 'orientation', 'column');           
    create table public.hologres_child1 partition of public.hologres_parent for values in('v1');
    create table public.hologres_child2 partition of public.hologres_parent for values in('v2');
    create table public.hologres_child3 partition of public.hologres_parent for values in('v3');
    commit;
    ```

-   示例2：在public schema下创建带主键的分区父表和对应的分区子表。

    ```
    begin;
    create table public.hologres_parent_2(
      a text , 
      b int, 
      c timestamp, 
      d text,
      primary key(a,b)
      )
      partition by list(a);
    call set_table_property('public.hologres_parent_2', 'orientation', 'column');
    create table public.holo_child_1 partition of public.hologres_parent_2 for values in('v1');
    create table public.holo_child_2 partition of public.hologres_parent_2 for values in('v2');
    create table public.holo_child_3 partition of public.hologres_parent_2 for values in('v3');
    commit;
    ```


