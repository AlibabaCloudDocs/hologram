---
keyword: [ALTER SCHEMA, Hologres]
---

# ALTER SCHEMA

ALTER SCHEMA语句用于修改数据库中的模式。本文为您介绍ALTER SCHEMA的用法及使用限制。

## 使用限制

-   交互式分析Hologres仅支持修改模式的名称。
-   使用`ALTER SCHEMA RENAME`修改模式名称时，系统将转移原Schema中的所有表至重命名的Schema中，同时会保留原Schema。

## 语法

修改模式名称的SQL语句如下。

```
ALTER SCHEMA oldschema RENAME TO newschema; 
```

## 示例

```
ALTER SCHEMA my_schema RENAME TO new_schema;
SET search_path TO my_schema;  //进入原Schema。
SET search_path TO new_schema; //进入重命名的Schema。
```

