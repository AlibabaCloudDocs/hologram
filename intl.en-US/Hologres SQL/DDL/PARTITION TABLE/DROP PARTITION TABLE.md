---
keyword: [delete a partitioned table, Hologres]
---

# DROP PARTITION TABLE

You can execute the DROP PARTITION TABLE statement to delete a partitioned table. This topic describes how to execute the DROP PARTITION TABLE statement.

## Limits

If you delete a parent partitioned table, its child partitioned tables are also deleted.

You must specify the partitioned table that you want to delete in the statement.

## Syntax

The following syntax applies when you execute the DROP PARTITION TABLE statement:

```
DROP TABLE [ IF EXISTS ] table_name [, ...] ;
```

**Note:** You can execute the `DROP TABLE` statement to delete multiple tables at a time.

The following table describes the parameters in the statement.

|Parameter|Description|
|---------|-----------|
|IF EXISTS|-   If you specify the `IF EXISTS` parameter in the statement, a success response is returned regardless of whether the specified table exists.
-   If you do not specify the `IF EXISTS` parameter in the statement, the following error is thrown when the specified table does not exist: `ERROR: table "non_exist_table" does not exist`. |
|table\_name|The name of the table that you want to delete.|

## Examples

The following sample statement shows how to delete a partitioned table:

```
DROP TABLE hologres_child2;
```

