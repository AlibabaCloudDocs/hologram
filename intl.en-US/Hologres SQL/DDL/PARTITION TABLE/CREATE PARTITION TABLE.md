---
keyword: [create a partitioned table, DDL, Hologres]
---

# CREATE PARTITION TABLE

You can execute the CREATE PARTITION TABLE statement to create a partitioned table. This topic describes how to execute the CREATE PARTITION TABLE statement.

## Limits

-   Hologres allows you to import data to a child partitioned table rather than a parent partitioned table.

    **Note:** Realtime Compute for Apache Flink allows you to import data to a parent partitioned table in Hologres. For more information, see [t1997136.dita\#task\_1997136/section\_7av\_gr2\_kws](t1997136.dita#task_1997136/section_7av_gr2_kws).

-   Only TEXT, VARCHAR, and INT data can be used as partition keys.
-   You can create only one partitioned table for each partition.
-   The `PARTITION BY` clause supports only `list partitioning`. The partition key must be a single column.
-   The partitioned table must be in the same schema as the parent table.
-   If the table has a primary key, the partition key must be a subset of the primary key.

## Syntax

The following syntax applies when you execute the CREATE PARTITION TABLE statement:

```
// Create a parent partitioned table.
create table [if not exists] [schema_name.]table_name partition by list (column_name) ([
  {
   column_name column_type [column_constraints, [...]]
   | table_constraints
   [, ...]
  }
]);

// Create a child partitioned table.
create table [if not exists] [schema_name.]table_name partition of parent_table
  for values in (string_literal);
```

## Examples

The following sample code shows you how to create a partitioned table:

```
// Create a partitioned table that contains no primary key.
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

// Create a partitioned table that contains a primary key.
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

