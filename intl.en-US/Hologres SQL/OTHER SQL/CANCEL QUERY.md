---
keyword: [CANCEL QUERY, cancel data queries, Hologres]
---

# CANCEL QUERY

You can execute the CANCEL QUERY statement to cancel an ongoing data query. This topic describes how to use the CANCEL QUERY statement in Hologres.

## Syntax

The CANCEL QUERY statement uses the following syntax:

```
SELECT pg_cancel_backend(pid);
```

## Examples

1.  Execute the following SQL statement to query the process ID \(PID\) of the ongoing query:

    ```
    SELECT pid,query FROM pg_stat_activity WHERE length(query) >0;
    ```

2.  Execute the following SQL statement to cancel the query specified by the PID:

    ```
    select pg_cancel_backend(192910);
    ```


