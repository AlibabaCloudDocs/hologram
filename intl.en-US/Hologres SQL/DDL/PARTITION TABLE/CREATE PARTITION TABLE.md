---
keyword: [create partitioned tables, DDL, Hologres]
---

# CREATE PARTITION TABLE

You can execute the CREATE PARTITION TABLE statement to create a partitioned table. This topic describes how to execute the CREATE PARTITION TABLE statement.

## Limits

-   Hologres allows you to import data to a child table rather than a parent table.

    **Note:** Realtime Compute for Apache Flink allows you to import data to a parent table. For more information, see [t1880180.dita\#concept\_2457265](/intl.en-US/Data Access/Big Data/Realtime Compute/Realtime Compute write to a Hologres parent partition table.md).

-   Only TEXT, VARCHAR, and INT data can be used as partition keys.
-   You can create only one partitioned table for each partition.
-   The `PARTITION BY` clause supports only `list partitioning`. The partition key must be a single column.
-   The partitioned table must be in the same schema as the parent table.

## Syntax

The following syntax applies when you execute the CREATE PARTITION TABLE statement:

```
// Create a parent table.
CREATE TABLE [IF NOT EXISTS] [schema_name.]table_name PARTITION BY list (column_name) ([
  {
   column_name column_type [column_constraints, [...]]
   | table_constraints
   [, ...]
  }
]);

// Create a child table.
CREATE TABLE [IF NOT EXISTS] [schema_name.]table_name PARTITION OF <parent_table>
    FOR VALUES IN (string_literal);
```

## Example

Execute the following statements to create a partitioned table:

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

