---
keyword: [CANCEL QUERY, 取消查询, Hologres]
---

# CANCEL QUERY

CANCEL QUERY语句用于取消正在执行的查询。本文为您介绍在Hologres中CANCEL QUERY语句的用法。

## 语法

CANCEL QUERY命令的语法如下所示。

```
SELECT pg_cancel_backend(pid);
```

## 示例

1.  查询正在执行的query。

    ```
    SELECT pid,query FROM pg_stat_activity WHERE length(query) >0;
    ```

2.  寻找指定query的pid，并取消query。

    ```
    select pg_cancel_backend(192910);
    ```


