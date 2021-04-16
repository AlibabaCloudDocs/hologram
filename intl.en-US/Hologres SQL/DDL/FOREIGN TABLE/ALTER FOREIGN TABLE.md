---
keyword: [modify a foreign table, DDL statement, Hologres]
---

# ALTER FOREIGN TABLE

You can execute the ALTER FOREIGN TABLE statement to modify a foreign table. This topic shows you how to execute the ALTER FOREIGN TABLE statement to rename a foreign table, add columns to a foreign table, and delete columns from a foreign table.

## Limits

Hologres allows you to execute the ALTER FOREIGN TABLE statement only to rename a foreign table, add columns to a foreign table, and delete columns from a foreign table.

## Rename a foreign table

The following syntax applies when you execute the ALTER FOREIGN TABLE statement to rename a foreign table:

```
ALTER FOREIGN TABLE [ IF EXISTS ] name RENAME TO new_name;    
```

For example, you can execute the following statement:

```
ALTER FOREIGN TABLE test RENAME TO new_test_table; 
```

## Add a column to a foreign table

Hologres does not automatically update the schema of a created foreign table after columns are added to the mapping MaxCompute table. To query values of the added columns through the foreign table, you must manually add the columns to the foreign table.

**Note:** You cannot execute the following statement to add columns for multiple foreign tables in a schema at the same time. For more information about how to add columns for multiple foreign tables in a schema at the same time, see [IMPORT FOREIGN SCHEMA](/intl.en-US/Hologres SQL/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md).

The following syntax applies when you execute the ALTER FOREIGN TABLE statement to add a column to a foreign table:

```
ALTER FOREIGN TABLE IF EXISTS table_name ADD COLUMN new_column_name data_type;
```

For example, you can execute the following statement:

```
ALTER FOREIGN TABLE bank
 ADD COLUMN  cons_conf_idx float8,
 ADD COLUMN  euribor3m float8;
```

## Delete a column from a foreign table

The following syntax applies when you execute the ALTER FOREIGN TABLE statement to delete a column from a foreign table:

```
ALTER FOREIGN TABLE IF EXISTS table_name DROP COLUMN column_name;
```

For example, you can execute the following statement:

```
ALTER FOREIGN TABLE bank
 DROP COLUMN  cons_conf_idx,
 DROP COLUMN  euribor3m;
```

