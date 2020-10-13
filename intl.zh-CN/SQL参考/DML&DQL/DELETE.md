---
keyword: [DELETE, 删除表, Hologres]
---

# DELETE

DELETE语句用于删除目标表指定列的行数据。本文为您介绍在Hologres中DELETE语句的用法。

## 使用限制

Hologres暂不支持直接删除分区表父表。您需要删除具体的分区表子表后，才可以删除分区表父表。

## 语法

DELETE命令的语法如下所示。

```
DELETE FROM table_name [ * ] [ [ AS ] alias ]
    [ WHERE condition  ]
```

参数说明如下表所示。

|参数|描述|
|--|--|
|alias|别名。目标表的替代名称。|
|condition|删除目标表的条件。|

## 示例

删除表的示例语句如下。

```
CREATE TABLE delete_test (
    id INT PRIMARY KEY,
    a INT,
    b text 
);

INSERT INTO delete_test VALUES 
(1, 10, 'a'),
(2, 30, 'b'),
(3, 50,  ''),
(4, 70, null);

DELETE FROM delete_test AS dt WHERE dt.a = 10;
DELETE FROM delete_test AS dt WHERE dt.b is null;
DELETE FROM delete_test AS dt WHERE dt.b='';
```

更多关于DELETE的详情，请参见[PostgreSQL DELETE](https://www.postgresql.org/docs/10/sql-delete.html)。

