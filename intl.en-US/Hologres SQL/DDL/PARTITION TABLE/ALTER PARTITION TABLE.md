---
keyword: [modify a partitioned table, Hologres, DDL]
---

# ALTER PARTITION TABLE

This topic describes how to execute the ALTER PARTITION TABLE statement to modify a partitioned table.

## Syntax

You can execute one of the following statements to modify a partitioned table in Hologres:

```
ALTER TABLE [IF EXISTS] table_name RENAME to new_table_name;
ALTER TABLE [IF EXISTS] table_name ATTACH PARTITION new_partition_name FOR VALUES in (<string_literal>);
ALTER TABLE [IF EXISTS] table_name DETACH PARTITION paritition_name;
```

## Parameters

The following table describes the parameters in the ALTER TABLE statement that is used to modify a partitioned table.

|Parameter|Description|
|---------|-----------|
|`RENAME`|The clause that renames the partitioned table.|
|`ATTACH PARTITION new_partition_name FOR VALUES in (<string_literal>)`|The clause that attaches a table to a partitioned table as a partition. Take note of the following items:-   You must attach the table based on the partition policy and partition key of the partitioned table.
-   The table to be attached must have the same number of fields as the partitioned table. The data types of the fields must match.
-   The table to be attached must have all the NOT NULL constraints of the partitioned table.

If you attach a list partition that does not accept NULL values, add the NOT NULL constraint to the partition key, unless the primary key is an expression. |
|`DETACH PARTITION partition_name`|The clause that detaches a specified partition from a partitioned table.The detached partition still exists as a standalone table, but no longer has ties to the table from which it was detached. |

## Examples

The following code shows how to execute the ALTER TABLE statement to modify a partitioned table:

```
-- Rename a partitioned table.
alter table holo_test rename to my_holo_test;

--Attach the table named my_table as a partition of the table named holo_table.
alter table holo_table attach partition my_table for values in ('2015');

--Detach the child partitioned table holo_test from the parent partitioned table all_test and make the child partitioned table a standalone table.
alter table all_test detach partition holo_test; 
            
```

The following code shows how to replace an existing child partitioned table:

```
---Create a temporary table.
begin; 
drop table if exists "table_20210101_new";
create table "table_20210101_new" (
  "colA" integer not null,
  "colB" text not null,
  "colC" numeric not null,
  "ds" text not null,
  "process_time" timestamptz not null default now()
);
call_set_table_property('table_20210101_new', 'orientation','column');
call_set_table_property('table_20210101_new', 'distribution_key','colA');
call_set_table_property('table_20210101_new', 'event_time_column','process_time');
commit;

---Import data to the temporary table.
insert into "table_20210101_new" select * from ... ;

---Replace an existing child partitioned table with the temporary table.
begin;
--Detach an existing child partitioned table from the parent partitioned table and make the child partitioned table a standalone table.
alter table table_parent DETACH PARTITION table_20210101;
--Rename the standalone table.
alter table table_20210101 RENAME to table_20210101_backup;
--Rename the temporary table as the original child partitioned table.
alter table table_20210101_new RENAME to table_20210101;
--Attach the new child partitioned table to the parent partitioned table.
alter table table_parent ATTACH PARTITION table_20210101 FOR VALUES in ("20210101");
commit;
```

