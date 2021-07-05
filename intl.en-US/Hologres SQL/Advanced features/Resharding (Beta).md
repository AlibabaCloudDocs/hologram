# Resharding \(Beta\)

Hologres provides the resharding feature. After you change the number of shards, you can use the resharding feature to rebalance the existing tables and data among shards, without the need to create tables and import data again. This simplifies the resharding procedure and delivers optimal performance. This topic describes how to use the resharding feature in Hologres.

## Background information

In Hologres, shards are used to improve the concurrency of data processing. You can set the number of shards to an appropriate value to improve the query or write performance. In most scenarios, the default number of shards for a Hologres instance can meet your requirements. You do not need to change the number of shards.

After a Hologres instance is scaled up, such as from 32 CPU cores to 128 CPU cores, the number of shards does not change for the existing databases in the Hologres instance. If you need to improve the performance of a database, we recommend that you increase the number of shards for the database. If you create a database in the Hologres instance, the number of shards is the default number for the current instance type. For more information about instance specifications and shards, see [Instance types](/intl.en-US/Manage Instances/Instance types.md).

After a Hologres instance is scaled up or down, the number of shards does not change for existing databases. You can execute statements to change the number of shards and import data again. Hologres provides the resharding feature. After you change the number of shards, you can use the resharding feature to rebalance the existing tables and data among shards, without the need to create tables and import data again. This simplifies the resharding procedure and delivers optimal performance.

## Limits

-   The resharding feature is supported only in Hologres V0.10 and later. You can check the version of your Hologres instance on the instance details page in the Hologres console. If the version of your instance is earlier than V0.10, [submit a ticket](https://workorder-intl.console.aliyun.com/) to update your instance.
-   Resharding is performed on one table at a time. When you use the resharding feature, you must stop writing data to the involved tables, such as writing data by using Flink or the data integration feature. You can still query data in these tables.
-   Resharding consumes a number of resources. We recommend that you use the resharding feature during off-peak hours.

## Procedure

To use the resharding feature, perform the following steps:

1.  **Optional. Query table groups.**

    Shards are bound to a table group. When you change the number of shards, you must create a table group. Before you change the number of shards, you can use the following statement to check the number of shards for existing table groups.

    **Note:** Table groups occupy fixed resources in the system. We recommend that you create at most three table groups in a Hologres instance based on actual needs.

    ```
    select * from hologres.hg_table_group_properties;
    ```

    The following figure shows the execution results. In this example, the current database has a table group named `xx_tg_default`, the number of shards is 20 for the table group, and the table group contains nine internal tables.

    ![View the number of shards](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8356542261/p262524.png)

2.  **Change the number of shards.**

    If the number of shards for existing table groups does not meet your requirements, you can create a table group and set the number of shards as needed. You cannot change the number of shards for the existing table groups. When you create a table group to change the number of shards, take note of the following items:

    -   All newly created tables belong to this table group unless you execute the following statement again to create another table group.
    -   The original tables and data belong to the original table group and are not automatically migrated to the new table group.
    -   The original table group becomes ineffective only after the original tables and data are migrated to the new table group or deleted.
    -   Sample code

        ```
        call hg_create_table_group('table_group_name',shard_count);
        ```

    -   Parameters

        |Parameter|Description|Example|
        |---------|-----------|-------|
        |table\_group\_name|The name of the table group that you want to create. The name of the new table group must be different from that of the original table group.|new\_tg|
        |shard\_count|The number of shards that you want to set.|60|

    -   Examples

        ```
        -- Create a table group named new_tg and set the number of shards to 60.
        call hg_create_table_group('new_tg',60);
        ```

3.  **Migrate tables.**

    After you change the number of shards, you can migrate the original tables to the new table group. You can execute the following statement to migrate tables, without the need to create tables and import data. This implements auto resharding for tables.

    **Note:**

    -   When you migrate tables, you must stop writing data to the tables. You can still query data in these tables.
    -   The original tables are automatically deleted from the original table group after they are migrated to the new table group. If multiple table groups are created based on your business requirements, we recommend that you set the number of shards to an appropriate value for each table group.
    -   When you migrate partitioned tables, you need only to migrate parent partitioned tables.
    -   Sample code

        ```
        call hg_update_table_shard_count('table_name','table_group_name')
        ```

    -   Parameters

        |Parameter|Description|Example|
        |---------|-----------|-------|
        |table\_name|The name of the table to be migrated. You can execute the statement to migrate only one table at a time. If you need to migrate multiple tables, execute the statement multiple times. If the table to be migrated is a partitioned table, you need only to migrate the parent partitioned table. For more information about how to query tables, see Step 1: [Optional. Query table groups](#section_3nz_ej4_s2d).|new\_table|
        |table\_group\_name|The name of the table group to which the table is migrated.|new\_tg|


