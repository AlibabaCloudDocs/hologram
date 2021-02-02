---
keyword: [create a Hologres dimension table, ]
---

# Hologres dimension table

In the original product line, a Blink exclusive cluster is an independent computing cluster that is created on an Alibaba Cloud Elastic Compute Service \(ECS\) instance. This topic describes how to create a Hologres dimension table by using Blink-based Realtime Compute for Apache Flink in exclusive mode.

## Limits

Blink supports only Hologres tables that are created based on the row-oriented storage type. You can use Hologres tables as dimension tables and join them by using Holo-blink connectors.

When you create a table based on the row-oriented storage type, you must set a primary key and set the property of the primary key to clustering key. To create a Hologres table, use the following sample statement:

```
begin;
create table test(a int primary key, b text, c text[], d float8, e int8);
call set_table_property('test', 'orientation', 'row');
call set_table_property('test', 'clustering_key', 'a');
commit;
```

## DDL syntax

To create a Hologres dimension table, use the following sample data definition language \(DDL\) statement:

```
CREATE TABLE rds_dim_table(
 id INT,
 len INT,
 content VARCHAR,
 PRIMARY KEY (id),
 PERIOD FOR SYSTEM_TIME // Define the change period of the dimension table. This indicates that the dimension table is continuously updated.
) with (
  type='hologres',
  'dbname'='<yourDbname>',  // The name of the Hologres database to be connected to.
  'tablename'='<yourTablename>', // The name of the Hologres table to which data is to be written.
  'username'='<yourUsername>', // The AccessKey ID of your Alibaba Cloud account.
  'password'='<yourPassword>', // The AccessKey secret of your Alibaba Cloud account.
  'endpoint'='<yourEndpoint>' // The virtual private cloud (VPC) endpoint of your Hologres instance.
);
```

The following table describes the parameters contained in the with object.

|Parameter|Description|Required|
|---------|-----------|--------|
|type|The type of the dimension table. Set the value to hologres.

|Yes|
|dbname|The name of the Hologres database to be connected to.|Yes|
|tablename|The name of the Hologres table to which data is to be written.|Yes|
|username|The AccessKey ID of your Alibaba Cloud account. You can obtain the AccessKey ID on the [AccessKey Management](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb) page.

|Yes|
|password|The AccessKey secret of your Alibaba Cloud account. You can obtain the AccessKey secret on the [AccessKey Management](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb) page.

|Yes|
|endpoint|The VPC endpoint of your Hologres instance. You can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance), go to the details page of the instance that you want to view, and then find the endpoint on the **Configurations** tab.

|Yes|

## Parameters in the cache object

The SQL statement that is used to create a Hologres dimension table may contain parameters in the cache object. The following table describes the parameters contained in the cache object.

|Parameter|Description|Required|Example|
|---------|-----------|--------|-------|
|cache|The policy that is used to cache data.|No|Hologres supports the following three cache policies: -   None: indicates that no data is cached. This is the default cache policy.
-   LRU: indicates that only the specified data in the dimension table is cached. The system searches among the cache each time it receives a data record from the source table. If the system does not find the record in the cache, the system searches for the data record in the physical dimension table.

If you use this cache policy, you must set the cacheSize and cacheTTLMs parameters.

-   ALL: indicates that all the data in the dimension table is cached. Before the system runs a job, the system loads all the data in the dimension table to the cache. If you need to retrieve data from the dimension table, the system searches among the cache. If a cache miss occurs, the corresponding keys do not exist in your cache. In this case, all the data is cached again after the cache expires.

If the remote table stores only a small volume of data and a large number of missing keys exist, we recommend that you set this parameter to ALL. If you use the ON clause to join the dimension table with a source table and the dimension table does not include the keys that are specified in the join conditions, missing keys occur.

If you use this cache policy, you must set the cacheTTLMs and cacheReloadTimeBlackList parameters.


 **Note:**

-   If you set the cache parameter to ALL, you must increase the memory space of the node where the dimension table is joined with another table. This is because the system asynchronously loads the data from the dimension table. The increased memory size is twice the size of the data in the remote table.
-   If a dimension table stores a large volume of data and the cache parameter is set to ALL, an out of memory \(OOM\) error may occur or a full garbage collection \(GC\) may be time-consuming. To resolve this issue, you can use the following methods:
    -   If the cache parameter can be set to ALL for a dimension table, enable the partitionedJoin feature.

        -   For Blink versions earlier than V3.6.0, the full data of the dimension table is loaded for each concurrent job by default.
        -   For Blink V3.6.0 and later, the partitionedJoin feature is supported if you set the cache parameter to ALL.
After you enable the partitionedJoin feature, only the required data is cached for each concurrent job.

    -   Use an HBase or ApsaraDB RDS dimension table that uses key-value pairs to store data. |
|cacheSize|The maximum number of data records that can be cached.|No|This parameter is used only if you set the cache parameter to LRU. Default value: 10000.|
|cacheTTLMs|The timeout period of the cache. Unit: milliseconds.|No|-   If you set the cache parameter to LRU, the cacheTTLMs parameter specifies the timeout period of the cache. By default, the cached data does not expire.
-   If you set the cache parameter to ALL, the cacheTTLMs parameter specifies the interval at which the system refreshes the cache. By default, the cache is not refreshed. |
|partitionedJoin|Specifies whether to query data from partitions.|No|Valid values: -   false: This value is the default value.
-   true |
|async|Specifies whether to asynchronously read data.|No|Valid values: -   false: This value is the default value.
-   true |

## Example

To create a Hologres dimension table and import data, use the following sample statements:

```
create table randomSource (a int, b VARCHAR, c VARCHAR) with (type = 'random');

create table test (
      a int,
    b VARCHAR,
    c VARCHAR,
    PRIMARY KEY (a, b), PERIOD FOR SYSTEM_TIME
) with (
  type = 'hologres',
  'dbname'='<yourDbname>', // The name of the Hologres database to be connected to.
  'tablename'='<yourTablename>', // The name of the Hologres table to which data is to be written.
  'username'='<yourUsername>', // The AccessKey ID of your Alibaba Cloud account.
  'password'='<yourPassword>', // The AccessKey secret of your Alibaba Cloud account.
  'endpoint'='<yourEndpoint>' // The VPC endpoint of your Hologres instance.
);

create table print_sink (
  a int,
  b VARCHAR
) with (type = 'print', `ignoreWrite` = 'false');

insert into print_sink
select randomSource.a, test.b from randomSource
LEFT JOIN test FOR SYSTEM_TIME AS OF PROCTIME()
on randomSource.a = test.a and randomSource.b = test.b;
```

## Mappings between data types

For more information about the mappings between data types in Blink and Hologres, see [t1345987.dita\#concept\_1597919](/intl.en-US/Hologres SQL/Data types/Data types.md).

