---
keyword: [Hologres, instance type, CPU core]
---

# Instance types

The CPU core and memory resources predefined for Hologres instances vary depending on instance types. In Hologres, computing and storage are isolated. Therefore, storage resources are irrelevant to instance types. This topic describes the instance types that are available for use in Hologres. You can dynamically adjust the configuration of a Hologres instance based on your business requirements. For example, you can upgrade or downgrade the configuration of the Hologres instance and separately manage the computing resources and storage resources.

## Terms

The resources that are used to run a Hologres instance include the process resources for metadata management, the computing resources for query services, and the import link resources and caching services for optimizing data writing. Hologres offers all services based on container technologies. It implements high-performance parallel computing capabilities by using multiple parallel container-based compute nodes.

Hologres predefines the maximum number of connections and the number of shards for a Hologres instance based on the instance type. The default settings are fine-tuned and optimized to meet the requirements in most cases. **The maximum number of connections cannot be changed, and the number of shards can be changed by using functions. After a Hologres instance is scaled up, the maximum number of connections is automatically adjusted. For a database created before the instance is scaled up, the number of shards remains unchanged. You must manually change the number of shards for the database. For a database created after the instance is scaled up, the number of shards is determined based on the new instance type.**

After a Hologres instance is scaled up, more CPU cores are available. This improves the performance of concurrent queries. In most cases, you do not need to change the number of shards. If you need to write more data to Hologres, you can increase the number of shards. This improves the write throughout. For a row-oriented table, if the number of shards increases, data in this table can be easier read.

## Default resource configurations

Hologres predefines the maximum number of connections and the number of shards for a Hologres instance based on the instance type. The following table describes the default resource configuration for each instance type.

**Note:** These configurations are applicable to Hologres V0.8 and later.

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

\(1\) Maximum number of total connections = Maximum number of connections for a single frontend host × Number of frontend hosts.

## Query and manage the connections to a Hologres instance

Hologres allows you to query and manage the connections to a Hologres instance.

-   Query the maximum number of connections.

    After you create a Hologres instance and connect it to a development tool, you can execute the following statement to query the maximum number of connections that can be established to the Hologres instance:

    ```
    show max_connections;
    ```

-   Manage connections.

    Hologres reserves connections for the superuser of an instance. When the number of connections to the instance reaches the upper limit specified for the instance type, the superuser can connect to Hologres and execute SQL statements to query idle connections and release them. The superuser can also upgrade the configuration of the instance based on business needs. For more information about how to query idle connections and release connections, see the "Connections" section in the [Metrics of Hologres](/intl.en-US/Monitoring And Alerting/Metrics of Hologres.md) topic.


## Query and change the number of shards for a Hologres instance

After a Hologres instance is scaled up, more CPU cores are available. This improves the performance of concurrent queries. In most cases, you do not need to change the number of shards. If you need to write more data to Hologres, you can increase the number of shards. This improves the write throughout.

For a row-oriented table, if the number of shards increases, data in this table can be easier read. For information about how to view and change the number of shards for a Hologres instance, see [t2069037.md\#]().

