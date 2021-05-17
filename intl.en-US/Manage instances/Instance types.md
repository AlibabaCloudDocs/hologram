---
keyword: [Hologres, instance type, CPU core]
---

# Instance types

Hologres instances of different instance types provide different CPU core and memory resources. In Hologres, an architecture that separates computing from storage is used. In this case, storage resources are irrelevant to the instance types. This topic describes the instance types of Hologres instances. You can dynamically adjust the instance type based on your business needs. For example, you can upgrade or downgrade the specifications of a Hologres instance and separately manage computing resources and storage resources.

## Basic concepts

Resources that are used to run a Hologres instance include process resources for metadata management, computing resources for query services, import link resources for optimizing data writing, and caching services. Hologres offers all services based on container technologies and implements high-performance parallel computing capabilities by using multiple parallel container-based compute nodes.

Hologres provides the default maximum number of connections and the number of pre-allocated shards based on the instance type of a Hologres instance. The default values of these parameters are adjusted and optimized to meet the requirements in most cases. In these parameters, **the maximum number of connections cannot be changed and the number of shards can be adjusted by using functions. While you scale the system in or out, the maximum number of connections is adjusted, but the number of shards is kept as default**.

After the system is scaled out, more CPU cores are available and a better query concurrency capability can be provided. In most cases, the number of shards can be kept as default. If you need to write more data to Hologres, you can increase the number of shards. This improves the write throughout. In addition, if the number of shards in a row-oriented table is larger, data in this table can be easier to read by Hologres.

## Default instance resource table

Hologres provides the default maximum number of connections and the number of pre-allocated shards based on the instance type of a Hologres instance. The following table describes the default specifications of instance types.

**Note:** These specifications are applicable to Hologres V0.8 and later.

|Instance type|Default number of shards|Maximum number of total connections \(1\)|Number of connections reserved for the superuser|
|-------------|------------------------|-----------------------------------------|------------------------------------------------|
|32 CPU cores|20|128 \(64 × 2\)|6|
|64 CPU cores|40|256 \(128 × 2\)|6|
|96 CPU cores|60|384 \(192 × 2\)|6|
|128 CPU cores|80|512 \(171 × 3\)|9|
|160 CPU cores|100|640 \(160 × 4\)|12|
|192 CPU cores|120|768 \(154 × 5\)|15|
|256 CPU cores|160|1,024 \(171 × 6\)|18|
|400 CPU cores|240|1,600 \(160 × 10\)|30|
|512 CPU cores|320|2,048 \(171 × 12\)|36|

\(1\) Maximum number of total connections = Maximum number of connections from a single frontend host × Number of frontend hosts. The specifications of each host are specified in the parenthesis.

## Query and manage the default number of connections to a Hologres instance

Hologres allows you to query and manage the default number of connections to a Hologres instance.

-   Query the default number of connections.

    After you create a Hologres instance and connect it to a development tool, you can execute the following statement to query the default maximum number of connections to the Hologres instance:

    ```
    show max_connections;
    ```

-   Manage the connections.

    Hologres reserves connections for the superuser of an instance. When the number of connections reaches the default upper limit of the instance type, the superuser can connect to Hologres and execute SQL statements to query idle connections and release them, or can upgrade the specifications of the instance based on business needs. For more information about how to query idle connections and release connections, see [Connections](/intl.en-US/Monitoring and alerting/Metrics of Hologres.md).


## Query and change the number of shards pre-allocated for a Hologres instance

Hologres allows you to query the number of shards that are pre-allocated for the current Hologres instance. After you create a Hologres instance and connect it to a development tool, you can perform the following steps to query the number of shards:

1.  Query a table group.

    Query the table group to which a table belongs. The number of shards is the same for all tables in a table group. In the following sample statement, you must replace `table_name` with the name of the table that you want to query.

    ```
    select property_value from hologres.hg_table_properties where table_name = 'tmp' and property_key = 'table_group';
    ```

2.  Query the number of shards for the table group.

    In the query results, the value of the `shard_count` parameter indicates the current number of shards.

    ```
    select * from hologres.hg_table_group_properties;
    ```

    ![Number of shards](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1307790261/p239859.png)

3.  Change the number of shards.

    You can execute the following statement to change the number of shards for the table group. You can adjust the number of shards based on your business needs.

    ```
    call hg_update_database_property('shard_count', '60');
    ```

    After the statement is executed, a table group is created and is set as the default table group of the current database. The number of shards is 60 for this table group. After that, all newly created tables belong to this table group unless you execute the preceding statement again to create another one.


