# How do I specify a field name starting with a digit in an SQL statement?

Compatible with PostgreSQL, Hologres uses the same syntax as PostgreSQL and does not support the query of a field whose name starts with a digit.

If you want to query a field whose name starts with a digit in Hologres, enclose the field name in double quotation marks \(" "\). An example is as follows:

```
select bizdate,"1_day_active_users","7_day_active_users" from t_active_users;
```

