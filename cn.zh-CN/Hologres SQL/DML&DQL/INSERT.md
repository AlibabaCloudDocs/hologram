---
keyword: [插入数据, Hologres, INSERT]
---

# INSERT

INSERT语句用于插入新的行数据至表中。本文为您介绍在交互式分析Hologres中如何使用INSERT插入数据。

## 命令格式

您可以插入一个或多个由表达式指定的行，以及插入来自一个查询的零行或多行数据至Hologres。语句如下。

```
INSERT INTO table [( column [, ...] )]
   VALUES ( {expression}  [, ...] ) 
   [, ...] | query}
```

## 参数说明

参数说明如下表所示。

|参数|描述|
|--|--|
|table|已创建的表名称。表名称也可以使用`Table.Schema`格式。 |
|column|table表中的某个列的名称。您也可以使用子域名或者数组下标限定列名称。（指向一个组合列的某些列中插入会让其他域为空）。 |
|expression|赋予相应列的表达式或值。|
|query|需要被插入行的SELECT查询语句。 语法详情请参见[SELECT](/cn.zh-CN/Hologres SQL/DML&DQL/SELECT.md)语句。|

目标列的名称可以以任意顺序列出。如果没有给出列名列表，则有两种确定目标列的可能性。第一种是以被声明的顺序列出该表的所有列。另一种可能性是，如果VALUES子句或者query只提供N个列，则以被声明的顺序列出该表的前N列。VALUES子句或者query提供的值会被从左至右关联到这些显式或者隐式给出的目标列。

每一个没有出现在显式或者隐式列列表中的列都将被默认填充，如果为该列声明过默认值则用默认值填充，否则用空值填充。如果任意列的表达式不是正确的数据类型，将会尝试自动转换类型。

## 使用示例

Hologres支持使用如下INSERT语句写入数据：

-   `INSERT INTO VALUES`

    ```
    INSERT INTO rh_holo2mysqltest (cate_id, cate_name) VALUES
        (3, 'true'),
        (3, 'fale'),
        (3, 'trxxue'),
        (3, 'x'),
        (4, 'The Dinner Game');
    ```

-   `INSERT INTO SELECT`

    ```
    INSERT INTO test2
    SELECT 2,'two';
    ```

-   插入分区表数据

    插入分区表数据，您需要将数据直接插入已经建好的子表中，插入时对应的分区字段值要和子表分区字段值一致。

    ```
    --在public schema下创建不带主键的分区父表和对应的分区子表
    begin;
    create table public.hologres_parent(
      a text, 
      b int, 
      c timestamp, 
      d text
    ) 
      partition by list(a);
    call set_table_property('public.hologres_parent', 'orientation', 'column');           
    create table public.hologres_child1 partition of public.hologres_parent for values in('v1');
    create table public.hologres_child2 partition of public.hologres_parent for values in('v2');
    create table public.hologres_child3 partition of public.hologres_parent for values in('v3');
    commit;
    --插入分区表数据
    INSERT INTO public.hologres_child1 values('v1',1,now(),'a')
    ```


