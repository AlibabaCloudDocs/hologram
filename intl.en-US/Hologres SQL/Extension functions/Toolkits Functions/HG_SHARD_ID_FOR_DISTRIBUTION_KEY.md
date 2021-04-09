---
keyword: [HG\_SHARD\_ID\_FOR\_DISTRIBUTION\_KEY, Hologres, tool function]
---

# HG\_SHARD\_ID\_FOR\_DISTRIBUTION\_KEY

You can use the HG\_SHARD\_ID\_FOR\_DISTRIBUTION\_KEY function to query the shard IDs of a specified key or a composite key. This topic describes how to use the HG\_SHARD\_ID\_FOR\_DISTRIBUTION\_KEY function in Hologres.

## Syntax

```
SELECT HG_SHARD_ID_FOR_DISTRIBUTION_KEY ( 'schemaname.tablename' , columnname [...] ] ) 
FROM schemaname.tablename
```

## Parameters

The following table describes the parameters of the HG\_SHARD\_ID\_FOR\_DISTRIBUTION\_KEY function.

|Parameter|Description|
|---------|-----------|
|schemaname|The name of the schema of the table for which you want to query shard IDs.|
|tablename|The name of the table for which you want to query shard IDs.|
|columnname|The name of the key or the composite key for which you want to query shard IDs.|

## Examples

To query the shard IDs of a specified key or a composite key, perform the following steps:

1.  Create an extension. Execute the following statement to create an extension. This statement takes effect only in the current database. You need only to execute the statement once. If you switch to another database, the superuser must execute the statement again.

    ```
    create extension hg_toolkit;
    ```

2.  Use the HG\_SHARD\_ID\_FOR\_DISTRIBUTION\_KEY function to query the shard IDs of a key in a table. In this example, the function is used to query the shard IDs of the l\_shipmode field in the table named lineitem.

    ```
    SELECT  HG_SHARD_ID_FOR_DISTRIBUTION_KEY ( 'public.lineitem', l_shipmode)
    FROM public.lineitem ;
    ```

3.  View the execution results.

    ```
     hg_shard_id_for_distribution_key 
    ----------------------------------
                                   24
                                   24
                                   24
                                   24
                                   31
                                   24
                                   12
                                   25
                                   25
                                   25
    (10 rows)
    ```


