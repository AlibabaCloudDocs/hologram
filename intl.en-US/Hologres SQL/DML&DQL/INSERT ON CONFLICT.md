---
keyword: [INSERT ON CONFLICT, processing policy for duplicate data during data insertion, Hologres]
---

# INSERT ON CONFLICT

You can use the INSERT ON CONFLICT statement to specify the policy that is used to deal with conflicts when you insert the specified columns in a row that shares the same primary key value with an existing row. This topic describes how to use the INSERT ON CONFLICT statement in Hologres.

## Limits

-   In the `INSERT ON CONFLICT` statement, you must specify all primary key columns in the ON CONFLICT clause.
-   This statement cannot be executed if the version of the Hologres instance is earlier than the required version. You can execute the following statement or [submit a ticket](https://workorder-intl.console.aliyun.com/) to update the version of your Hologres instance to V0.8.

    ```
    set hg_experimental_enable_insert_on_conflict = on; 
    ```


## Scenarios

The `INSERT ON CONFLICT` statement is applicable to scenarios in which data is written by executing SQL statements.

If you are using Data Integration or Flink to insert a row that shares the same primary key value with an existing row, you can perform the following operations to update the existing row or ignore the new row:

-   Use the Data Integration service of DataWorks to write data to Hologres.

    The `INSERT ON CONFLICT` statement is integrated in Data Integration. For more information about how the statement works, see [Hologres Writer](). If you use Data Integration, you must set the following parameters:

    -   If you use a batch sync node, set the Write Mode parameter to **SDK\(Fast write\)** and set the **Write conflict strategy** parameter to **Ignore \(Ignore\)** or **Cover \(Overwrite\)** based on your business logic.
    -   If you use a real-time sync node, set the **Write conflict strategy** parameter to **Ignore \(Ignore\)** or **Cover \(Overwrite\)**.
    **Note:** Tables that are created in Hologres must contain primary keys. This way, data in these tables can be updated during data synchronization.

-   Use Flink to write data to Hologres.

    If you use Flink to write data to a Hologres table that has a primary key, the value of the **Write conflict strategy** parameter is **Cover \(Overwrite\)** by default.


## Syntax

The `INSERT ON CONFLICT` statement uses the following syntax:

```
INSERT INTO table_name [ AS alias ] [ ( column_name [, ...] ) ]
    {  VALUES ( { expression } [, ...] ) [, ...] | query }
    [ ON CONFLICT [ conflict_target ] conflict_action ]

where conflict_target is pk

    ON CONSTRAINT constraint_name

and conflict_action is one of:

    DO NOTHING
    DO UPDATE SET { column_name = { expression } |
                    ( column_name [, ...] ) = ( { expression } [, ...] ) |
                    ( column_name [, ...] ) = ( sub-SELECT )
                  } [, ...]
              [ WHERE condition ]
```

The following table describes the parameters in the statement.

|Parameter|Description|
|---------|-----------|
|DO NOTHING|If you insert the specified columns in a row that shares the same primary key value with an existing row, ignore the row to be inserted.|
|DO UPDATE|If you insert the specified columns in a row that shares the same primary key value with an existing row, update the existing row.|
|expression|The expression that is executed on the corresponding column. You can refer to PostgreSQL documentation to set the expression..

For example, the expression is `excluded.column_name`. In this expression, excluded is the alias of the source table from which data is to be inserted. The column\_name variable specifies the name of the column based on which data is to be inserted to the destination table. For example, if the column\_name parameter specifies the Nth column in the destination table. The expression excluded.column\_name indicates the Nth column in the source table. The expression excluded\* indicates to insert data of all columns in the source table to the destination table. Make sure that the columns in the source table are in the same order as the columns specified in the DDL statement of the destination table. |

## Examples

-   The following examples show you how to use the `INSERT ON CONFLICT` statement:

    ```
    create table conflict_1(
      a int not null primary key, 
      b int ,
      c int);
    insert into conflict_1 values
    (1,1,1),
    (2,3,4);
    
    create table conflict_2(
      a int not null primary key, 
      b int ,
      c int);
    insert into conflict_2 values(1,5,6);
    -- If a row in Table conflict_1 and a row in Table conflict_2 contain the same primary key value, replace the data of a specified column in the row in Table conflict_1 with that in Table conflict_2. 
    insert into conflict_1 (a,b) select a,b from conflict_2 on conflict(a) do update set b = excluded.b; 
    
    -- If a row in Table conflict_1 and a row in Table conflict_2 contain the same primary key value, insert the row in Table conflict_2 to Table conflict_1. 
    insert into conflict_1 values(2,7,8) on conflict(a) do update set b = excluded.b, c = excluded.c where conflict_1.c = 4; 
    
    -- If a row in Table conflict_1 and a row in Table conflict_2 contain the same primary key value, the system ignores the row in Table conflict_2 to be inserted to Table conflict_1. The insertion fails. 
    insert into conflict_1 select * from conflict_2 on conflict(a) do nothing; 
    
    -- If no conflicting column is specified in the DO NOTHING clause, the primary key is used as the conflicting column. 
    insert into conflict_1 select * from conflict_2 on conflict do nothing; 
    
    -- Specify the name of a PRIMARY KEY constraint. 
    insert into conflict_1 select * from conflict_2 on conflict on constraint conflict_1_pkey do update set a = excluded.a;
    
    --- Update the entire row. 
    insert into tmp1_on_conflict values(1,2,3) on conflict(a) do update set (a, b ,c )= ROW(excluded.*); 
    ```

    ![conflict](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0461175261/p174954.png)

-   The following examples show you how to optimize the `INSERT ON CONFLICT` statement for row-oriented tables:

    Hologres optimizes the INSERT ON CONFLICT statement for row-oriented tables. We recommend that you specify columns in the UPDATE clause in the same order as those in the INSERT clause and update all columns.

    ```
    insert into conflict_1(a,b,c) select a,b,c from conflict_2 on conflict (a) do update set a = exculded.a, b = excluded.b, c= excluded.c;
    
    insert into fonflict_1(a,b,c) select a,b,c from conflict_2 on conflict (a) do update (a,b,c) = Row(excluded.*)
    ```


## Errors and troubleshooting

-   Error description

    The following error occurs when you execute the `INSERT ON CONFLICT` statement to insert data from a source table to a destination table.

    ![Common errors of the INSERT ON CONFLICT statement](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1461175261/p200644.png)

-   Cause

    Compatible with PostgreSQL, Hologres allows you to use the standard PostgreSQL syntax. If you use the standard PostgreSQL syntax to execute the `INSERT ON CONFLICT` statement to insert data from a source table to a destination table, make sure that the source table does not contain duplicate rows. If the source table contains duplicate rows, the preceding error occurs.

    **Note:** Duplication in a source table indicates that the source table contains duplicate rows. It does not indicate that the destination table contains the same row as the row to be inserted.

    The source table contains duplicate rows when the `INSERT ON CONFLICT` statement is executed. The following statement provides an example:

    ```
    insert into tmp1_on_conflict values(1,2,3),(1,2,3) on conflict(a) do update set (a, b ,c )= ROW(excluded.*);
    ```

-   Solution

    If the source table contains duplicate rows, you can execute the `set hg_experimental_affect_row_multiple_times_keep_first = on;` statement to retain only the first row in these duplicate rows.


