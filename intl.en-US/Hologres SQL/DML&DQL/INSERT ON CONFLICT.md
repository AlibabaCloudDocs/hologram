---
keyword: [INSERT ON CONFLICT, processing policy for duplicate rows, Hologres]
---

# INSERT ON CONFLICT

You can use the INSERT ON CONFLICT statement to specify the policy that is used to deal with conflicts when you insert a row that contains the same primary key value as an existing row based on specified columns. This topic shows you how to use the INSERT ON CONFLICT statement in Hologres.

## Limits

-   The condition of the `INSERT ON CONFLICT` statement must contain all primary keys.
-   This statement cannot be executed if the current Hologres instance version is earlier than the required version. You can execute the following statement or [submit a ticket](https://workorder-intl.console.aliyun.com/) to upgrade your Hologres instance to version 0.8.

    ```
    set hg_experimental_enable_insert_on_conflict = on; 
    ```


## Scenarios

The `INSERT ON CONFLICT` statement is applicable to scenarios in which data is written by executing SQL statements.

Assume that you are using Data Integration or Flink to insert a row that contains the same primary key value as an existing row. In this case, you can perform the following operations to update the existing row or ignore the new row:

-   Use the Data Integration service of DataWorks to write data to Hologres.

    Data Integration supports the `INSERT ON CONFLICT` statement. For more information about how the statement works, see [Hologres Writer](). If you use Data Integration, you must set the following parameters:

    -   If you use a batch sync node, set the Write mode parameter to **SDK\(Fast write\)** and set the **Write conflict strategy** parameter to **Ignore** or **Replace** based on your business logic.
    -   If you use a real-time sync node, set the **Write conflict strategy** parameter to **Ignore** or **Replace**.
    **Note:** Tables that are created in Hologres must contain primary keys. This way, data in these tables can be updated during data synchronization.

-   Use Flink to write data to Hologres.

    When you use Flink to write data to Hologres, the value of the **Write conflict strategy** parameter is **Replace** by default. You must set primary keys when you create a table in Hologres.


## Syntax

The `INSERT ON CONFLICT` statement has the following syntax:

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

The following table describes the parameters in the syntax.

|Parameter|Description|
|---------|-----------|
|DO NOTHING|If you insert a row based on specified columns and the row contains the same primary key value as an existing row, ignore the row to be inserted.|
|DO UPDATE|If you insert a row based on specified columns and the row contains the same primary key value as an existing row, update the existing row.|
|expression|The expression that is executed on the corresponding column. You can refer to the PostgreSQL documentation to set the expression. For more information, see [INSERT](https://www.postgresql.org/docs/11/sql-insert.html).

For example, the expression is `excluded.column_name`. In this expression, the excluded parameter specifies the alias of the source table from which data is to be inserted. The column\_name parameter specifies the name of the column based on which data is to be inserted into the destination table. Assume that the column\_name parameter specifies the Nth column in the destination table into which a row in the source table is to be inserted. The expression excluded.column\_name indicates the Nth column in the source table. |

## Example

The following example shows you how to use the `INSERT ON CONFLICT` statement:

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

insert into conflict_1 select * from conflict_2 on conflict(a) do update set b = excluded.b; // When a row in Table conflict_1 and a row in Table conflict_2 contain the same primary key value, replace the row in Table conflict_1 with that in Table conflict_2.

insert into conflict_1 values(2,7,8) on conflict(a) do update set b = excluded.b, c = excluded.c where conflict_1.c = 4; // When a row in Table conflict_1 and a row in Table conflict_2 contain the same primary key value, insert the row in Table conflict_2 into Table conflict_1.

insert into conflict_1 select * from conflict_2 on conflict(a) do nothing; // When a row in Table conflict_1 and a row in Table conflict_2 contain the same primary key value, the system directly ignores the row in Table conflict_2 to be inserted into Table conflict_1. This indicates that the insertion fails.

insert into conflict_1 select * from conflict_2 on conflict do nothing; // If the DO NOTHING clause does not specify a conflicting column, the default conflicting column is a primary key.

insert into conflict_1 select * from conflict_2 on conflict on constraint conflict_1_pkey do update set a = excluded.a; // Specify the name of a PRIMARY KEY constraint.

insert into tmp1_on_conflict values(1,2,3) on conflict(a) do update set (a, b ,c )= ROW(excluded. *); // Update the entire row.
```

## Errors and troubleshooting

-   Error description

    The following error occurs when you execute the `INSERT ON CONFLICT` statement on a source table.

-   Cause

    Compatible with PostgreSQL, Hologres allows you to use the standard PostgreSQL syntax. In the standard PostgreSQL syntax, a source table cannot contain duplicate rows when the `INSERT ON CONFLICT` statement is executed. If the table contains duplicate rows, the preceding error occurs.

    **Note:** Duplication in a source table indicates that the source table contains duplicate rows. It does not indicate that the destination table contains the same row as the row to be inserted.

    The source table contains duplicate rows when the `INSERT ON CONFLICT` statement is executed. The following statement provides an example:

    ```
    insert into tmp1_on_conflict values(1,2,3),(1,2,3) on conflict(a) do update set (a, b ,c )= ROW(excluded. *);
    ```

-   Solution

    If the source table contains duplicate rows, you can execute the `set hg_experimental_affect_row_multiple_times_keep_first = on;` statement to retain the first row in these duplicate rows.


