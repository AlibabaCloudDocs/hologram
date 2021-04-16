---
keyword: [create multiple foreign tables at a time, Hologres]
---

# IMPORT FOREIGN SCHEMA

You can execute the IMPORT FOREIGN SCHEMA statement to create multiple foreign tables at a time in Hologres. This topic describes the syntax and limits of the IMPORT FOREIGN SCHEMA statement.

## Limits

We recommend that you use the `LIMIT TO` clause to specify the source tables and enclose the table names in parentheses \(\) in the `IMPORT FOREIGN SCHEMA` statement. Otherwise, foreign tables are created in Hologres to map all the tables in the specified MaxCompute project.

## Syntax

The following sample code shows you how to use the IMPORT FOREIGN SCHEMA statement to create foreign tables in Hologres:

```
IMPORT FOREIGN SCHEMA remote_schema
    [ { LIMIT TO | EXCEPT } ( table_name [, ...] ) ]
    FROM SERVER odps_server
    INTO local_schema 
    [ OPTIONS ( option 'value' [, ... ] ) ]
```

## Parameters

The following table describes the parameters in the syntax.

|Parameter|Description|
|---------|-----------|
|remote\_schema|The name of the MaxCompute project where the source tables reside.|
|table\_name|The name of the MaxCompute table that the foreign table to be created maps.|
|server\_name|The name of the server where the MaxCompute tables are stored. Default value: **odps\_server**. The **odps\_server** server is created at the underlying layer of Hologres. For more information, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k). |
|local\_schema|The name of the schema where the Hologres foreign tables reside. Example: public.|
|options|Hologres supports the following two options:-   if\_table\_exist: specifies the operation to perform when a foreign table to be created is named the same as an existing foreign table in Hologres. Valid values:
    -   error: Do not create the foreign table that is named the same as an existing foreign table in Hologres. This is the default value.
    -   ignore: Skip the creation of the foreign table that is named the same as an existing foreign table in Hologres and create other foreign tables.
    -   update: Create the foreign table that is named the same as an existing foreign table in Hologres and use it to replace the existing foreign table.
-   if\_unsupported\_type: specifies the operation to perform when a foreign table to be created involves data types that are not supported by Hologres. Valid values:
    -   error: Return an error message indicating that the foreign tables fail to be created and list the tables that involve unsupported data types.
    -   skip: Skip the creation of the foreign tables that involve unsupported data types and list the skipped tables. This is the default value. |

**Note:** Hologres supports only foreign tables that are sourced from MaxCompute. The created foreign tables are named the same as the MaxCompute tables.

## Examples

In these examples, foreign tables are created in Hologres to map the tables in the public dataset named **public\_data** in MaxCompute.

-   Example 1: Create a foreign table in the schema named public. If the foreign table to be created is named the same as an existing foreign table in Hologres, update the existing table.

    ```
    IMPORT FOREIGN SCHEMA public_data LIMIT to
    (customer) 
      FROM server odps_server INTO PUBLIC options(if_table_exist 'update');
    ```

-   Example 2: Create multiple foreign tables at a time in the schema named public.

    ```
     IMPORT FOREIGN SCHEMA public_data LIMIT to(
      customer,
      customer_address,
      customer_demographics,
      inventory,item,
      date_dim,
      warehouse) 
      FROM server odps_server INTO PUBLIC options(if_table_exist 'update');
    ```

-   Example 3: Create a schema named holotest and create multiple foreign tables at a time in the schema.

    ```
    create schema testdemo;
    
    IMPORT FOREIGN SCHEMA public_data LIMIT to(
      customer,
      customer_address,
      customer_demographics,
      inventory,item,
      date_dim,
      warehouse) 
      FROM server odps_server INTO testdemo options(if_table_exist 'update');
      
    set search_path to testdemo;
    ```

-   Example 4: Create multiple foreign tables at a time in the schema named public. If a foreign table to be created is named the same as an existing foreign table in Hologres, an error is returned.

    ```
    IMPORT FOREIGN SCHEMA public_data LIMIT to
    (customer,
      customer_address) 
      FROM server odps_server INTO PUBLIC options(if_table_exist 'error');
    ```

-   Example 5: Create multiple foreign tables at a time in the schema named public. If a foreign table to be created is named the same as an existing foreign table in Hologres, skip the creation of this foreign table.

    ```
    IMPORT FOREIGN SCHEMA public_data LIMIT to
    (customer,
      customer_address) 
      FROM server odps_server INTO PUBLIC options(if_table_exist 'ignore');
    ```


## Create multiple foreign tables at a time in a visualized manner

Hologres allows you to create multiple foreign tables at a time in a visualized manner by using HoloWeb. For more information, see [Create multiple foreign tables at a time](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/MaxCompute Acceleration/Create multiple foreign tables at a time.md).

## Data type mappings

For more information about data type mappings between MaxCompute and Hologres when you create multiple foreign tables at a time, see [Data type mappings between MaxCompute and Hologres when you create multiple foreign tables at a time](/intl.en-US/Hologres SQL/Data types/Data types.md).

