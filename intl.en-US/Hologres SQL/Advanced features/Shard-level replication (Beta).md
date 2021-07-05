# Shard-level replication \(Beta\)

This topic describes how to use the shard-level replication feature in Hologres.

## Overview

In Hologres V0.10 and later, Hologres allows you to set the replica count of each shard for a table group to improve the availability of point queries for the table group. To enable the replication feature, you can set the replica count of each shard when you create a table group, or change the replica count of each shard for an existing table group.

When you set or change the replica count of each shard, take note of the following items:

-   In a Hologres instance, data is distributed by shard. Each shard manages different data. The data is not duplicated between different shards. A complete set of data consists of the data in all shards.
-   By default, each shard has only one replica. In this case, the value of the `replica_count` parameter is 1. This replica serves as the leader shard. You can adjust the replica count of each shard to replicate the data of each shard in more shard replicas. In this case, shard replicas other than the leader shard serve as follower shards.
-   The leader shard is responsible for all read and write requests. When the leader shard is unavailable, the system automatically switches requests to follower shards for query. When you use a follower shard for query, the data may be queried at a latency in the range of 10 ms to 20 ms.
-   The default value of the replica\_count parameter is 1, which indicates that the replication feature is not enabled. A value greater than 1 indicates that the replication feature is enabled. We recommend that you set this parameter to 2. A greater value indicates that more resources are required.

When the query service times out due to reasons such as a network timeout or an unavailable process, the system can switch user requests between shard replicas. The switchover is imperceptible to users. This way, users can query data in high availability mode. This data query mode is applicable only to scenarios in which row-oriented tables are queried based on the primary key.

## Limits

When you use the shard-level replication feature in Hologres, take note of the following limits:

-   This feature is supported only in Hologres V0.10 and later. You can check the version of your Hologres instance on the instance details page in the Hologres console. If the version of your instance is earlier than V0.10, [submit a ticket](https://workorder-intl.console.aliyun.com/) to update your instance.
-   The shard-level replication feature is applicable only to scenarios in which row-oriented tables are queried based on the primary key.

## Syntax

-   **Query the table groups in a database**

    To query the table groups in the current database, execute the following statement:

    ```
    select * from hologres.hg_table_group_properties ;
    ```

-   **Query the replica count of each shard for a table group**
    -   Sample code

        ```
        select property_value from hologres.hg_table_group_properties where tablegroup_name = 'table_group_name' and property_key = 'replica_count';
        ```

    -   Parameters

        |Parameter|Description|
        |---------|-----------|
        |table\_group\_name|The name of the table group that you want to query.|
        |replica\_count|The property that you want to query. Set the value to replica\_count.|

-   **Enable the replication feature**
    -   Sample code
        -   Read replicas are enabled at the database level. For each database, you need to enable read replicas only once. When you use the following statement to enable read replicas for a database, replace the `databasename` parameter with the name of the database:

            ```
             alter database <databasename> set hg_experimental_enable_read_replica = on;
            ```

        -   To enable the replication feature for a table group, execute the following statement:

            ```
            call hg_set_table_group_property ('table_group_name', 'replica_count', '2');
            ```

    -   Parameters

        |Parameter or function|Description|
        |---------------------|-----------|
        |hg\_experimental\_enable\_read\_replica|Specifies whether to enable read replicas for a database. For each database, you need to enable read replicas only once.         -   on: enables read replicas.
        -   off: disables read replicas. |
        |hg\_set\_table\_group\_property|The function that is used to set the replica count of each shard for a table group.         -   table\_group\_name: the name of the table group that you want to manage.
        -   replica\_count: the replica count of each shard for the table group.
        -   Feature switch: specifies whether to enable the replication feature. A value of 1 indicates that the replication feature is not enabled. Default value: 1. A value greater than 1 indicates that the replication feature is enabled. |

-   **Query data in high availability mode**

    This feature is applicable only to scenarios in which row-oriented tables are queried based on the primary key. Sample code:

    ```
    -- For example, the primary key columns of a table are pk1 and pk2.
    select * from your_table_name where pk1 = 'xx' and pk2 = 'xxx';
    ```

    In the preceding sample code, replace the `your_table_name` parameter with the name of the table that you want to query, and set the pk1 and pk2 parameters to the names of the primary key columns of the table.

-   **Switch between shard replicas to query data in high availability mode**
    -   Sample code

        ```
        -- Enable read replicas for a database.
        set hg_experimental_enable_read_replica = on;
        
        -- Specify the timeout period of requests for the leader shard. Unit: milliseconds. Default value: 60. If no response is returned for a request after the timeout period elapses, the system switches the request to a follower shard. 
        set hg_experimental_read_replica_leader_timeout_ms = 60;
        
        -- Specify the maximum duration for reading data from a replica after a request is switched from the leader shard to a follower shard. Unit: seconds. Default value: 60. After the maximum duration elapses, the system switches the request back to the leader shard. 
        set hg_experimental_read_replica_max_duration_sec = 30; 
                                    
        ```

    -   Parameters

        |Parameter|Description|
        |---------|-----------|
        |hg\_experimental\_enable\_read\_replica|Specifies whether to enable read replicas for a database. For each database, you need to enable read replicas only once.         -   on: enables read replicas.
        -   off: disables read replicas. |
        |hg\_experimental\_read\_replica\_leader\_timeout\_ms|The timeout period of requests for the leader shard. Unit: milliseconds. Default value: 60. If no response is returned for a request after the timeout period elapses, the system switches the request to a follower shard.|
        |hg\_experimental\_read\_replica\_max\_duration\_sec|The maximum duration for reading data from a replica after a request is switched from the leader shard to a follower shard. Unit: seconds. Default value: 60. After the maximum duration elapses, the system switches the request back to the leader shard. This enables automatic switchover between shard replicas for requests.|

-   **Disable the replication feature**
    -   Sample code

        ```
         -- Modify the replica_count parameter and disable the replication feature.
        call hg_set_table_group_property ('table_group_name', 'replica_count', '1');
        ```

    -   Parameters

        |Function|Description|
        |--------|-----------|
        |hg\_set\_table\_group\_property|The function that is used to set the replica count of each shard for a table group.         -   table\_group\_name: the name of the table group that you want to manage.
        -   replica\_count: the replica count of each shard for the table group.
        -   Feature switch: specifies whether to enable the replication feature. A value of 1 indicates that the replication feature is not enabled. Default value: 1. A value greater than 1 indicates that the replication feature is enabled. |


