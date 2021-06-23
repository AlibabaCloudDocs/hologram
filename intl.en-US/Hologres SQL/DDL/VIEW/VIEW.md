---
keyword: [create a view, Hologres]
---

# VIEW

Hologres allows you to create a view for an internal table or a foreign table. You can create a view based on a single table, multiple tables, or another view. This topic describes how to create a view in Hologres.

## Usage notes

When you create and view a view, take note of the following items:

-   If you create a view based on a single table and modify the data in the view, the data of the source table is updated accordingly. If you modify the data of the source table, the data in the view is also updated. When you use a single-table view, we recommend that you modify the data in the view with caution. This prevents the data of the source table from being modified and ensures that your business is not affected.
-   If you create a view based on multiple tables, you cannot modify the data in the view.

## Create a view

You can use the following syntax to create a view:

```
CREATE [TEMP | TEMPORARY] VIEW view_name AS
SELECT column1, column2.....
FROM table_name
WHERE [condition];
```

If you specify the TEMPORARY or TEMP keyword, a temporary view is created. The temporary view is automatically deleted at the end of a session.

## Create a view for an internal table

1.  Create an internal table. For example, you can execute the following statements:

    ```
    create table holo_test (
      amount decimal(10, 2), 
      rate decimal(10, 2)
    );
    insert into holo_test values 
    (12.12,13.13),
    (14.14,15.15),
    (16.16,17.17),
    (17.1,17),
    (18.01,19);
    ```

2.  Create a view for the internal table and query the table data. For example, you can execute the following statements:

    ```
    create view holo_view as select * from holo_test;
    
    select * from holo_view;
     amount | rate
    --------+-------
      12.12 | 13.13
      14.14 | 15.15
      16.16 | 17.17
      17.10 | 17.00
      18.01 | 19.00
    (5 rows)
    ```


## Create a view for a foreign table

1.  Create a foreign table. For example, you can execute the following statements:

    ```
    create foreign table if not exists holo_foreign_test (
      amount decimal(10, 2), 
      rate decimal(10, 2)) 
      server odps_server 
      options(project_name '<projectname>', table_name '<odps_name>')
      );
      
    select * from holo_foreign_test limit 2;
    ```

2.  Create a view for the foreign table and query the table data. For example, you can execute the following statements:

    ```
    create view foreign_view as select * from holo_foreign_test;
    
    select * from foreign_view limit 2;
     amount | rate
    --------+-------
      12.12 | 13.13
      14.14 | 15.15
    ```


## Create a federated view for an internal table and a foreign table

1.  Create a federated view for an internal table and a foreign table and query the table data. For example, you can execute the following statements:

    ```
    create view view1 as select * from holo_view union all  select * from foreign_view;
    
    select * from view1;
     amount | rate
    --------+-------
      12.12 | 13.13
      14.14 | 15.15
      16.16 | 17.17
       17.1 |    17
      18.01 |    19
      12.12 | 13.13
      14.14 | 15.15
      16.16 | 17.17
      17.10 | 17.00
      18.01 | 19.00
      12.12 | 13.13
      14.14 | 15.15
      16.16 | 17.17
       17.1 |    17
      18.01 |    19
      12.12 | 13.13
      14.14 | 15.15
      16.16 | 17.17
       17.1 |    17
      18.01 |    19
    (20 rows)
    ```


## Delete a view

You can use the following syntax to delete a view:

```
drop view <view_name>;
```

## Query all views and the DDL statements of the views

You can use the following syntax to query all of your views. If you query all of your views on the PostgreSQL client, you can also run the `\dv` command to query the views.

```
-- Execute the following statement:
SELECT n.nspname as "Schema",
  c.relname as "Name",
  CASE c.relkind WHEN 'r' THEN 'table' WHEN 'v' THEN 'view' WHEN 'm' THEN 'materialized view' WHEN 'i' THEN 'index' WHEN 'S' THEN 'sequence' WHEN 's' THEN 'special' WHEN 'f' THEN 'foreign table' WHEN 'p' THEN 'table' WHEN 'I' THEN 'index' END as "Type",
  pg_catalog.pg_get_userbyid(c.relowner) as "Owner"
FROM pg_catalog.pg_class c
     LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
WHERE c.relkind IN ('v','')
      AND n.nspname <> 'pg_catalog'
      AND n.nspname <> 'information_schema'
      AND n.nspname !~ '^pg_toast'
  AND pg_catalog.pg_table_is_visible(c.oid)
ORDER BY 1,2;
```

You can use the following syntax to query the DDL statement of a view:

```
create extension hg_toolkit;
select public.hg_dump_script('viewname');
```

