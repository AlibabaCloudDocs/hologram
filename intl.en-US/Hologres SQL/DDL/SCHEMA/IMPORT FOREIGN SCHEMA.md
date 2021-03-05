---
keyword: [create multiple foreign tables at a time, Hologres]
---

# IMPORT FOREIGN SCHEMA

You can execute the IMPORT FOREIGN SCHEMA statement to create multiple foreign tables at a time in Hologres. This topic describes the syntax and limits of the IMPORT FOREIGN SCHEMA statement.

## Limits

We recommend that you use the `LIMIT TO` clause to specify the source tables and enclose the table names in parentheses \(\) in the `IMPORT FOREIGN SCHEMA` statement. Otherwise, foreign tables are created in Hologres to map all the tables in the specified MaxCompute project.

## Syntax

The following syntax applies when you execute the IMPORT FOREIGN SCHEMA statement:

```
IMPORT FOREIGN SCHEMA remote_schema
    [ { LIMIT TO | EXCEPT } ( table_name [, ...] ) ]
    FROM SERVER server_name
    INTO local_schema 
    [ OPTIONS ( option 'value' [, ... ] ) ]
```

The following table describes the parameters in the syntax.

|Parameter|Description|
|---------|-----------|
|remote\_schema|The name of the MaxCompute project where the source tables reside.|
|table\_name|The name of the MaxCompute table that the foreign table to be created maps.|
|server\_name|The name of the server where the MaxCompute tables are stored.You can select the **odps\_server** server that is created at the underlying layer of Hologres. For more information, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k). |
|options|Hologres supports the following two options:-   if\_table\_exist: specifies the operation to perform when a foreign table to be created is named the same as an existing foreign table in Hologres. Valid values:
    -   error: Do not create the foreign table that is named the same as an existing foreign table in Hologres. This is the default value.
    -   ignore: Skip the creation of the foreign table that is named the same as an existing foreign table in Hologres and create other foreign tables.
    -   update: Create the foreign table that is named the same as an existing foreign table in Hologres and use it to replace the existing foreign table.
-   if\_unsupported\_type: specifies the operation to perform when a foreign table to be created contains data types that are not supported by Hologres. Valid values:
    -   error: Return an error message indicating that the foreign tables fail to be created and list the tables that contain unsupported data types.
    -   skip: Skip the creation of the foreign tables that contain unsupported data types and list the skipped tables. This is the default value. |

**Note:** Hologres supports only foreign tables that are sourced from MaxCompute. The created foreign tables are named the same as the MaxCompute tables.

## Examples

The following sample code shows you how to use the IMPORT FOREIGN SCHEMA statement to create foreign tables in Hologres:

```
// Create a foreign table for the public schema.
IMPORT FOREIGN SCHEMA <odpsproject_name> LIMIT TO (bank_data) FROM server odps_server INTO PUBLIC; 

// Create a schema named testdemo and create foreign tables for it.
CREATE Schema testdemo;
IMPORT FOREIGN SCHEMA odps_4_holoworkshop LIMIT TO (customer,lightning_cat_industry) FROM server odps_server INTO testdemo;
SET search_path TO testdemo;
\d

// Specify an option.
IMPORT FOREIGN SCHEMA <odpsproject_name> LIMIT TO (customer) FROM server odps_server INTO testdemo options( if_table_exist 'error');
IMPORT FOREIGN SCHEMA <odpsproject_name> LIMIT TO (customer) FROM server odps_server INTO testdemo options( if_table_exist 'ignore');
IMPORT FOREIGN SCHEMA <odpsproject_name> LIMIT TO (customer) FROM server odps_server INTO testdemo options( if_table_exist 'update');
```

## Data type mappings

For more information about data type mappings between MaxCompute and Hologres when you create multiple foreign tables at a time, see [Data type mappings between MaxCompute and Hologres when you create multiple foreign tables at a time](/intl.en-US/Hologres SQL/Data types/Data types.md).

