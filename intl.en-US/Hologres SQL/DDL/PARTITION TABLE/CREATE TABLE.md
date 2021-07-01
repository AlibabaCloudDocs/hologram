---
keyword: [create a partitioned table, DDL, Hologres]
---

# CREATE TABLE

This topic describes how to execute the CREATE TABLE statement to create a partitioned table.

## Description

A parent partitioned table is divided into different child partitioned tables based on partition key values. Data in the child partitioned tables is publicly available. Unless otherwise stated, parent tables and child tables mentioned in this topic all refer to parent partitioned tables and child partitioned tables.

Before you use a parent table, you must create child tables in advance. You can execute the `CREATE TABLE` statement to create a partitioned table.

The child tables of a parent table use different types of file storage. To query data in a partitioned table, you must specify a partition. This way, the system does not need to scan all the partitions. This helps the system find the queried file and improves efficiency. In most cases, a fact table is divided into different child tables by date.

## Limits

-   Hologres allows you to import data to a child table rather than a parent table.

    **Note:** Realtime Compute for Apache Flink allows you to import data to a parent table. For more information, see [t1997136.dita\#task\_1997136/section\_7av\_gr2\_kws](t1997136.dita#task_1997136/section_7av_gr2_kws).

-   Partition fields must be of the TEXT, VARCHAR, or INT type.
-   Each partition rule can be used to create only one partitioned table.
-   The `PARTITION BY` clause supports only `list partitioning`. The partition key must be a single field.``
-   A parent table and its child tables must be in the same schema.
-   If a partitioned table has a primary key, the partition key must be a subset of the primary key.

## Usage notes

-   If you want to import data from a database, we recommend that you do not use partitioned tables. If you use partitioned tables in this scenario, excessive partitioned tables may cause wastes of I/O resources. To resolve this issue and implement index-based query acceleration, you can specify the commonly used partition fields as the segment key.
-   If the number of data entries to be written to the partition of each day is less than 100 million, we recommend that you do not use partitioned tables. If you use partitioned tables in this scenario, the size of each partitioned table is too small. In this case, partitioned tables do not have clear advantages in query acceleration. To resolve this issue, you can specify a coarser granularity.
-   If you need to frequently replace the data of a specified day by performing TRUNCATE operations, we recommend that you use partitioned tables. If you use partitioned tables in this scenario, you can perform TRUNCATE operations in a more efficient way, without the need to delete a large amount of data.

## Syntax

The CREATE TABLE statement that is used to create a partitioned table uses the following syntax:

```
-- Create a parent table.
create table [if not exists] [schema_name.]table_name partition by list (column_name) ([
  {
   column_name column_type [column_constraints, [...]]
   | table_constraints
   [, ...]
  }
]);

-- Create a child table.
create table [if not exists] [schema_name.]table_name partition of parent_table
  for values in (string_literal);
```

## Parameters

The following table describes the parameters in the CREATE TABLE statement that is used to create a partitioned table.

|Parameter|Description|
|---------|-----------|
|if not exists|The clause that indicates that if a table with the same name already exists, the system does not return an error but notifies you that the table already exists.|
|schema\_name|The name of the schema in which the table resides. If you create parent and child tables in the same schema, you do not need to specify a schema name. If you create parent and child tables across schemas, you must specify a schema name.|
|table\_name|The name of the parent or child table to be created.|
|column\_name|The name of the field to be created in the new table.|
|column\_type|The data type of the field.|
|column\_constraints|The name of the column constraint.|
|table\_constraints|The name of the table constraint.|
|parent\_table|The name of the parent table of the child table.|
|string\_literal|The partition key.|

## Examples

The following examples show how to execute the CREATE TABLE statement to create partitioned tables:

-   Example 1: Create a parent table that does not have a primary key and its child tables in the public schema.

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

-   Example 2: Create a parent table that has a primary key and its child tables in the public schema.

    ```
    begin;
    create table public.hologres_parent_2(
      a text , 
      b int, 
      c timestamp, 
      d text,
      ds text
      primary key(ds,b)
      )
      partition by list(ds);
    call set_table_property('public.hologres_parent_2', 'orientation', 'column');
    create table public.holo_child_1 partition of public.hologres_parent_2 for values in('20201215');
    create table public.holo_child_2 partition of public.hologres_parent_2 for values in('20201216');
    create table public.holo_child_3 partition of public.hologres_parent_2 for values in('20201217');
    commit;
    ```


## View all child tables

You can view all child tables of a specified parent table by using one of the following methods:

-   View all child tables of a specified parent table in a visualized manner by using HoloWeb.
-   Execute the following SQL statement to view all child tables of a specified parent table. In the SQL statement, you can change parent\_table\_name to the name of the specified parent table.

    ```
    SELECT
        nmsp_parent.nspname AS parent_schema,
        parent.relname      AS parent,
        nmsp_child.nspname  AS child_schema,
        child.relname       AS child
    FROM pg_inherits
        JOIN pg_class parent            ON pg_inherits.inhparent = parent.oid
        JOIN pg_class child             ON pg_inherits.inhrelid   = child.oid
        JOIN pg_namespace nmsp_parent   ON nmsp_parent.oid  = parent.relnamespace
        JOIN pg_namespace nmsp_child    ON nmsp_child.oid   = child.relnamespace
    WHERE parent.relname='parent_table_name'; 
    ```


