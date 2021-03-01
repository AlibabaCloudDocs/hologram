---
keyword: [创建视图, Hologres]
---

# VIEW

Hologres支持基于内部表及外部表创建视图VIEW，您可以使用单张表、多张表或者其他视图来创建视图。本文为您介绍在Hologres中如何创建视图。

## 创建视图

创建视图的语法格式如下。

```
CREATE [TEMP | TEMPORARY] VIEW view_name AS
SELECT column1, column2.....
FROM table_name
WHERE [condition];
```

如果TEMPORARY或者TEMP被指定，视图会被创建为一个临时视图。当前会话结束时将会自动删掉临时视图。

## 创建内部表视图

1.  创建内部表，示例语句如下。

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

2.  基于内部表创建视图并查询表数据，示例语句如下。

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


## 创建外部表视图

1.  创建外部表，示例语句如下。

    ```
    create foreign table if not exists holo_foreign_test (
      amount decimal(10, 2), 
      rate decimal(10, 2)) 
      server odps_server 
      options(project_name '<projectname>', table_name '<odps_name>')
      );
      
    select * from holo_foreign_test limit 2;
    ```

2.  基于外部表创建视图并查询表数据，示例语句如下。

    ```
    create view foreign_view as select * from holo_foreign_test;
    
    select * from foreign_view limit 2;
     amount | rate
    --------+-------
      12.12 | 13.13
      14.14 | 15.15
    ```


## 创建内部表及外部表的联合视图

1.  基于内部表和外部表创建联合视图并查询表数据，示例语句如下。

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


## 删除视图

删除视图的语法格式如下。

```
drop view <view_name>;
```

## 查看所有视图和视图的DDL

您可以执行如下命令查看所有视图，如果您通过psql客户端查看所有视图，也可以执行`\dv`进行查看。

```
--sql命令
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

您可以执行如下命令查看VIEW的具体DDL：

```
create extension hg_toolkit;
select hg_dump_script('viewname');
```

