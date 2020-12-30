# FAQ about SQL statements

## How do I specify a field name that starts with a digit in an SQL statement?

Compatible with PostgreSQL, Hologres uses the same syntax as PostgreSQL and does not support the query of a field whose name starts with a digit.

If a field starts with a digit, you must enclose the field in double quotation marks \(""\). The following statement provides an example:

```
select bizdate,"1_day_active_users","7_day_active_users" from t_active_users;
```

## What can I do if an error occurs when I execute the TRUNCATE TABLE statement?

-   Error description

    An `ERROR: TRUNCATE TABLE is not supported now.` error occurs when I execute the `TRUNCATE TABLE <table name>;` statement.

-   Cause

    The current Hologres instance version is an earlier version.

-   Solution

    To upgrade your Hologres instance to version 0.8 or later, [submit a ticket](https://workorder-intl.console.aliyun.com/).


## What can I do if an error occurs when I execute the INSERT ON CONFLICT statement?

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


