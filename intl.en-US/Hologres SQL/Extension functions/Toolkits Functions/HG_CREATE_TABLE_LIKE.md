---
keyword: [create a table, HG\_CREATE\_TABLE\_LIKE, Hologres]
---

# HG\_CREATE\_TABLE\_LIKE

This topic describes the HG\_CREATE\_TABLE\_LIKE function and its parameters. This topic also provides sample code to show you how to use the function in Hologres.

## Introduction

The HG\_CREATE\_TABLE\_LIKE function is used to create a table based on the fields that are returned in a query result. You can use this function only to create a table but cannot copy data in the fields to the table. You must manually import the data to the newly created table. Syntax:

```
CALL HG_CREATE_TABLE_LIKE ( 'table_name', 'query_sql' [, 'partition_clause'])
```

## Parameters

The HG\_CREATE\_TABLE\_LIKE function includes the `table_name`, `query_sql`, and `partition_clause` parameters. The following table describes the parameters.

|Parameter|Description|
|---------|-----------|
|table\_name|The name of the table to be created. The table name can be schema-qualified. The name can contain only lowercase letters, uppercase letters, digits, and underscores \(\_\). It must start with a letter.

 If the name contains special characters, enclose the name in double quotation marks \(" "\). Uppercase letters are treated as lowercase letters because the value is not case-sensitive. |
|query\_sql|The SQL statement that is used to perform the query. If the SQL statement contains multiple single quotation marks \('\), you can escape the single quotation marks \('\) in one of the following ways based on your requirements: -   Use a terminal or a development tool such as Java Data Base Connectivity \(JDBC\) to add two dollar signs \($$\) preceding and following the SQL statement. That is, you can rewrite the SQL statement in the format of `$$query_sql$$`. For more information about other development tools, see [Common Development Tools](/intl.en-US/Common Development Tools/Overview.md).

    ```
CALL HG_CREATE_TABLE_LIKE ('table_name', $$query_sql$$ [, 'partition_clause'])
    ```

-   Use the SQL editor in the HoloWeb console to add single quotation marks \('\) preceding and following the content in single quotation marks \('\) in the SQL statement. For more information about how to use the SQL editor, see [SQL Editor](/intl.en-US/Common Development Tools/HoloWeb/SQL Editor/Manage an SQL query task.md). |
|partition\_clause|The clause that is used to create a partitioned table. This parameter is optional.|

## Examples

The following examples show you how to use the HG\_CREATE\_TABLE\_LIKE function:

-   Create a common table.

    ```
    // Use Field a and Field b in Table holotest to generate Table my_table.
    CALL HG_CREATE_TABLE_LIKE ('my_table', 'select a, b from holotest'); 
    ```

-   Create a partitioned table.

    ```
    // Use Field a and Field b in Table holotest to generate Table my_table. Specify Field a as a partition field of Table my_table.
    CALL HG_CREATE_TABLE_LIKE ('my_table', 'select a, b from holotest', 'partition by list (a)' );
    ```

-   Add single quotation marks \('\) preceding and following the content in single quotation marks \('\) in an SQL statement.

    ```
    CALL HG_CREATE_TABLE_LIKE ('holo_table', 'select id,name from my_test where ds =''2020-10-10''' );
    ```

-   Write an SQL statement in the format of `$$query_sql$$` to escape single quotation marks \('\).

    ```
    CALL HG_CREATE_TABLE_LIKE ('holo_table', $$select id,name from my_test where ds ='2020-10-10'$$ );                                     
    ```


