---
keyword: [创建表, HG\_CREATE\_TABLE\_LIKE, Hologres]
---

# HG\_CREATE\_TABLE\_LIKE

本文将为您介绍在交互式分析Hologres中HG\_CREATE\_TABLE\_LIKE函数的具体用法，包括函数简介、参数说明和使用示例，方便您快速了解和使用。

## 函数简介

HG\_CREATE\_TABLE\_LIKE用于创建一个以查询结果字段组成的表。该函数只能用于创建表，不能同步复制数据，数据信息需要您手动导入。函数命令格式如下：

```
CALL HG_CREATE_TABLE_LIKE ( 'table_name', 'query_sql' [, 'partition_clause'])
```

## 参数说明

HG\_CREATE\_TABLE\_LIKE函数的参数包括`table_name`、`query_sql`、`partition_clause`，具体说明如下表所示。

|参数|描述|
|--|--|
|table\_name|表名称。您也可以使用Schema限定表名称。表名称只能是小写英文字母（a~z）、大写英文字母（A~Z）、数字以及下划线（\_）的组合，并且以字母开头。

如果表名称有特殊字符，则需要使用双引号（""）转义。由于系统对大小写不敏感，大写字母（A~Z）会被认为是小写字母（a~z）。 |
|query\_sql|执行查询的SQL语句。如果SQL语句中有较多单引号，您可以根据实际使用情况通过如下方式对单引号进行转义：-   使用终端或者JDBC等开发工具，推荐您按照如下示例，在SQL语句前后添加两个美元符号（$$），通过`$$query_sql$$`改写，实现单引号转义。更多其他开发工具的使用请参见[连接开发工具](/cn.zh-CN/连接开发工具/概述.md)。

    ```
CALL HG_CREATE_TABLE_LIKE ('table_name', $$query_sql$$ [, 'partition_clause'])
    ```

-   使用HoloWeb中的SQL编辑器，请对SQL语句单引号中的内容再加单引号进行转义。SQL编辑器的使用请参见[SQL编辑器](/cn.zh-CN/连接开发工具/HoloWeb/SQL编辑器/SQL窗口.md)。 |
|partition\_clause|分区表的分区子句。该参数为可选参数。|

## 使用示例

如下示例将指导您使用HG\_CREATE\_TABLE\_LIKE函数进行一些简单操作。

-   创建一个普通表

    ```
    //将holotest表的a、b字段生成表my_table。
    CALL HG_CREATE_TABLE_LIKE ('my_table', 'select a, b from holotest'); 
    ```

-   创建一个分区表

    ```
    //将holotest表的a、b字段生成表my_table，并且a为分区字段。
    CALL HG_CREATE_TABLE_LIKE ('my_table', 'select a, b from holotest', 'partition by list (a)' );
    ```

-   使用单引号对查询语句单引号中的内容进行转义

    ```
    CALL HG_CREATE_TABLE_LIKE ('holo_table', 'select id,name from my_test where ds =''2020-10-10''' );
    ```

-   使用`$$query_sql$$`对单引号进行转义

    ```
    CALL HG_CREATE_TABLE_LIKE ('holo_table', $$select id,name from my_test where ds ='2020-10-10'$$ );                                     
    ```


