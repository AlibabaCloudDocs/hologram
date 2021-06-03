---
keyword: [optimize performance, MaxCompute table, best practice]
---

# Optimize the performance of querying MaxCompute tables in Hologres

This topic describes how to optimize the performance of querying MaxCompute tables in Hologres.

Seamlessly integrated with MaxCompute at the underlying layer, Hologres allows you to use the following methods to query MaxCompute data in an accelerated manner:

-   **Create a foreign table that is mapped to the external table to be queried**

    After a foreign table is created, you can directly query the mapped external table. This method does not require data export and import, or redundant storage. This method is applicable to a table whose size is less than 100 GB. Determine whether to use this method based on the volume of the data to be scanned at a time, but not the size of the queried fields.

-   **Import data from the external table to Hologres**

    To analyze and compute large amounts of data from a MaxCompute table, you can create an internal table in Hologres and import data from the MaxCompute table to the internal table. You can specify an appropriate distribution key for the internal table to accelerate queries based on your business requirements.

    Compared with the preceding method, this method enables faster queries. We recommend that you use this method to query the data of a table whose size is 100 GB or larger. This method is also applicable to complex queries such as queries by index, data updates, and data insertions.

    For more information about how to import data from a MaxCompute table to Hologres, see [Import data from MaxCompute to Hologres by executing SQL statements](/intl.en-US/Data Access/Batch synchronization/MaxCompute/Import data from MaxCompute to Hologres by executing SQL statements.md).


Some other methods are also available for you to optimize the performance of querying MaxCompute tables in Hologres based on your business requirements. These methods include optimizing the query statement, optimizing MaxCompute tables, allocating appropriate resources, and appropriately setting parameters.

## Optimize the query statement

When you query an external table, you can use the following methods to optimize the query statement to avoid a full table scan:

-   Use the `SELECT a FROM xx` statement instead of the `SELECT * FROM xx` statement. This way, the query range is narrowed down.
-   Add partition filter conditions or reduce the number of partitions to be scanned.

## Optimize MaxCompute tables

You can use the following methods to optimize MaxCompute tables to improve the performance of querying MaxCompute tables in Hologres:

-   **Convert MaxCompute tables to hash cluster tables**

    Hash cluster tables can help optimize bucket pruning, aggregation, and storage.

    When you create a MaxCompute table, use the `CLUSTERED BY` clause to specify the hash key. Then, MaxCompute performs the hash operation on the specified columns and distributes data to each bucket based on the hash values. We recommend that you select columns with fewer duplicate key values as the hash key.

    If no hash key is specified for an existing table, execute the following statement to specify the hash key:

    ```
    ALTER TABLE table_name [CLUSTERED BY (col_name [, col_name, ...]) [SORTED BY (col_name [ASC | DESC] [, col_name [ASC | DESC] ...])] INTO number_of_buckets BUCKETS]
    ```

    The `ALTER TABLE` statement is applicable to specifying the hash key for existing tables.

    After the hash key is specified, data is stored to new partitions in compliance with hash clustering. Then, execute the `INSERT OVERWRITE` statement to convert the MaxCompute table to a hash cluster table.

    **Note:**

    -   The `INSERT INTO` statement is not supported by hash clustering. You can add data only by using the `INSERT OVERWRITE` statement.
    -   MaxCompute Tunnel cannot be used to upload data to a range cluster table.
-   **Merge small files**

    Excessive small files may slow down the query.

    You can execute the following statement in Hologres to view the number of files that match the query:

    ```
    explain analyze <query>;
    ```

    The file\_count parameter in the output indicates the number of MaxCompute files that match the query. If large numbers of small files are matched, you can merge the small files.


## Allocate appropriate resources

After you purchase a Hologres instance, the system allocates resources based on the instance type. If your business deeply relies on accelerated queries of MaxCompute tables, you can contact technical support or [submit a ticket](https://workorder-intl.console.aliyun.com/) to allocate resources based on your business requirements.

## Appropriately set parameters

When external tables are queried, Hologres automatically sets some parameters to improve the concurrency of reading data, thus improving query efficiency. If you have special requirements, you can modify these parameters. The default values of these parameters are proven to be the most appropriate based on internal tuning and experiments. Therefore, we recommend that you use the default values unless necessary.

```
-- Set the number of data entries to be read at a time from a MaxCompute table. Default value: 8192. 
set hg_experimental_query_batch_size = xx

-- Set the size of each shard of a MaxCompute table. Default value: 64. Unit: MB. The shard size affects the concurrency. If a table is large in size, you can increase the value of this parameter to prevent excessive shards from deteriorating query performance. 
set hg_experimental_foreign_table_split_size = xx 

-- Set the maximum number of queries to be performed at a time in MaxCompute. Default value: 128. To prevent one query from affecting other queries and avoid errors caused by system unavailability, we recommend that you set this parameter to a small value. 
set hg_experimental_foreign_table_executor_max_dop = 64;
```

## Adopt a new engine for external table queries \(beta\)

Hologres V0.10 and later adopt a new engine to query MaxCompute data. Compared with the versions earlier than V0.10, Hologres V0.10 and later improve query performance by about 30% to 100%.

-   **Limits**
    -   This feature is supported only by Hologres V0.10 and later. You can view the version of your Hologres instance on the instance details page in the Hologres console. If the version of your instance is earlier than V0.10, [submit a ticket](https://workorder-intl.console.aliyun.com/) to update your instance.
    -   This feature applies only to the queries of MaxCompute ORC tables. Other files such as CFile files are not supported.
    -   To achieve better acceleration performance, make sure that MaxCompute data types are properly mapped to Hologres data types. For more information, see [Data type mappings between MaxCompute and Hologres when you create a foreign table](/intl.en-US/Hologres SQL/Data types/Data types.md).
-   **Usage**

    After you update your Hologres instance to V0.10 or later, you can execute the following statements to enable the new engine for external table queries:

    ```
    -- Enable the new engine for the current session.
    set hg_experimental_enable_access_odps_orc_via_holo = on;
    
    -- Enable the new engine for a specified database.
    alter database <databasename> set hg_experimental_enable_access_odps_orc_via_holo = on;
    ```


