---
keyword: [CREATE TABLE LIKE, Hologres, ]
---

# CREATE TABLE LIKE

CREATE TABLE LIKE语句用于创建一个同Select Query结果相同的表。本文为您介绍CREATE TABLE LIKE的用法。

## 使用限制

使用`CREATE TABLE LIKE`创建的表不会自动同步源表的数据。

## 普通表

1.  **命令格式**

    在Hologres中，普通表create table like的命令格式如下：

    ```
    CALL hg_create_table_like('table_name', 'select_query');
    ```

    **说明：** 通过调用hg\_create\_table\_like，系统会根据selec\_query结果的schema创建一个表名为table\_name的表，但不会插入任何数据。

2.  **参数说明**
    -   table\_name：要创建表的表名，只支持固定字符串，不支持字符拼接或函数生成等。
    -   select\_query：select格式的查询语句。只支持固定字符串，不支持字符拼接或函数生成等。
3.  **使用示例**

    在交互式分析中create table like的示例用法如下：

    1.  创建一个同源表结构相同的表

        ```
        CREATE TABLE src_table(a int, b text);
        CALL hg_create_table_like('new_table', 'select * from src_table');
        ```

        ![643](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6999029951/p91113.png)

    2.  创建一个在源表的基础上再增加字段的表

        ```
        CREATE TABLE test_table(a int, b text);
        
        CALL hg_create_table_like('holo_table', 'select *, 1 as c, ''a'' as d from test_table');
        ```

        ![644](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6999029951/p91114.png)


## 分区表

1.  **命令格式**

    在Hologres中，分区表的create table like的命令格式如下：

    ```
    CALL hg_create_table_like('table_name', 'select_query', 'partition_clause');
    ```

2.  **参数说明**
    -   table\_nameL：要创建表的表名，只支持固定字符串，不支持字符拼接或函数生成等。
    -   select\_query：select格式的查询语句。只支持固定字符串，不支持字符拼接或函数生成等。
    -   partition\_clause：分区相关的语法定义
3.  **使用示例**

    使用create table like 创建一张分区表示例如下：

    ```
    CREATE TABLE src_table(a int, b text);
    CALL hg_create_table_like('new_table', 'select *, 1 as c, ''a'' as d from src_table', 'partition by list(b)');
    
    CREATE TABLE new_table_child1 PARTITION OF new_table FOR VALUES IN(1);
    
    \d new_table
    
    \d new_table_child1 
    ```

    ![645](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6999029951/p91115.png)


## 设置表属性

在交互式分析中，使用create table like创建新表之后，可以结合set\_table\_property使用为新表设置更多的表属性。

```
CREATE TABLE src_table(a int, b text);

BEGIN;
CALL hg_create_table_like('new_table', 'select *, 1 as c, ''a'' as d from src_table');
CALL set_table_property('new_table', 'distribution_key', 'b');
CALL set_table_property('new_table', 'orientation', 'column');
CALL set_table_property('new_table', 'bitmap_columns', 'b');
CALL set_table_property('new_table', 'dictionary_encoding_columns', 'b');
CALL set_table_property('new_table', 'time_to_live_in_seconds', '86400');
COMMIT;

\d new_table
```

![646](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6999029951/p91116.png)

更多关于表属性的设置可以参见[设置表属性](/intl.zh-CN/SQL参考/DDL/TABLE/CREATE TABLE.md)。

## 使用限制

查询语句中的每一个目标列都要有一个不重复的别名，否则建表语句就会生成同列名的语句导致执行报错。示例如下：

```
CALL hg_create_table_like('new_table', 'select *, 1 as c, ''a'' as c from src_table');
ERROR:  column "c" specified more than once
CONTEXT:  SQL statement "create table new_table (
"a"     integer,
"b"     text,
"c"     integer,
"c"     text
);"
PL/pgSQL function hg_create_table_like(text,text) line 22 at EXECUTE
```

![647](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6999029951/p91117.png)

