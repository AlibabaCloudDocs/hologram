---
keyword: [ALTER SCHEMA, Hologres]
---

# ALTER SCHEMA

You can execute the ALTER SCHEMA statement to modify a schema in a database. This topic describes how to use the ALTER SCHEMA statement and the limits on using the statement.

## Limits

-   You can change only the name of a schema.
-   After you execute the `ALTER SCHEMA RENAME` statement, all tables in the schema with the original name are moved to the schema with the new name. The schema with the original name still exists.

## Syntax

To use the ALTER SCHEMA statement to change the name of a schema, use the following syntax:

```
ALTER SCHEMA oldschema RENAME TO newschema; 
```

## Examples

```
ALTER SCHEMA my_schema RENAME TO new_schema;
SET search_path TO my_schema;  // Go to the schema with the original name.
SET search_path TO new_schema; // Go to the schema with the new name.
```

