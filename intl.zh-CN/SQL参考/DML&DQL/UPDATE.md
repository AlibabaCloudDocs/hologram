---
keyword: [UPDATE, 更新表, Hologres]
---

# UPDATE

UPDATE语句用于更新目标表指定列的行数据。本文为您介绍在Hologres中UPDATE语句的用法。

## 使用限制

-   Hologres不支持更新Shard Key。
-   Hologres不支持直接更新分区表父表，您只能更新具体的分区表子表。

## 语法

UPDATE命令的语法如下所示。

```
UPDATE table [ * ] [ [ AS ] alias ]
    SET { column = { expression } |
          ( column [, ...] ) = ( { expression } [, ...] ) } [, ...]
    [ FROM from_list ]
    [ WHERE condition ]
```

参数说明如下表所示。

|参数|描述|
|--|--|
|alias|别名。目标表的替代名称。|
|expression|表达式|
|condition|更新目标表的条件。|

## 示例

更新表的示例语句如下。

```
CREATE TABLE update_test (
  a text primary key, 
  b int not null, 
  c text not null, 
  d text);  

INSERT INTO update_test VALUES ('b1', 10, '', '');

UPDATE update_test SET b = b + 10 where a = 'b1';
UPDATE update_test SET c = 'new_' || a, d = null where b = 20;
UPDATE update_test SET (b,c,d) = (1, 'test_c', 'test_d'); 

CREATE TABLE tmp(a int);
INSERT INTO tmp VALUES (2);
UPDATE update_test SET b = tmp.a FROM tmp;
```

更多关于UPDATE命令的详情，请参见[PostgreSQL官网文档](https://www.postgresql.org/docs/9.1/sql-update.html)。

