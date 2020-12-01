# SQL statement FAQ

## How do I specify a field name that starts with a digit in an SQL statement?

Compatible with PostgreSQL, Hologres uses the same syntax as PostgreSQL and does not support the query of a field whose name starts with a digit.

If a field starts with a digit, you must enclose the field in double quotation marks \(""\). The following statement provides an example:

```
select bizdate,"1_day_active_users","7_day_active_users" from t_active_users;
```

## What can I do if an error occurs when I execute the TRUNCATE TABLE statement?

-   Issue

    When I executed the `TRUNCATE TABLE <table name>;` statement, an `ERROR: TRUNCATE TABLE is not supported now.` error occurs.

-   Cause

    The current Hologres version is too old.

-   Solution

    [Submit a ticket](https://workorder-intl.console.aliyun.com/) to obtain Hologres version 0.8 or later.


