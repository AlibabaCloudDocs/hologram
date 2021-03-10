---
keyword: [Hologres, binlog]
---

# Subscribe to Hologres binlogs \(Beta\)

This topic describes how to subscribe to Hologres binlogs.

## Usage notes

Before you subscribe to binlogs in Hologres, take note of the following items:

-   Only Hologres V0.9 and later support binlog subscription. If the version of your Hologres instance is earlier than V0.9, [submit a ticket](https://workorder-intl.console.aliyun.com/) or join the Hologres DingTalk group for technical support.
-   Hologres supports subscription to the binlogs of a single table for both row-oriented tables and column-oriented tables. The following table describes the types of Hologres binlog subscription that are supported for different versions of Flink.

    |Flink version|Subscription to Hologres binlogs of row-oriented tables|Subscription to Hologres binlogs of column-oriented tables|
    |-------------|-------------------------------------------------------|----------------------------------------------------------|
    |Realtime Compute|Supported|Supported|
    |Flink Serverless|Not supported|Not supported|
    |Open source Flink|Not supported|Not supported|


## Enable binary logging

-   Feature overview

    By default, binary logging is disabled in Hologres. You can enable this feature by setting the table properties binlog.level and binlog.ttl. Theoretically, if your tables are frequently updated, the cost of binary logging for column-oriented tables is greater than the cost of binary logging for row-oriented tables. Therefore, we recommend that you enable binary logging for row-oriented tables.

-   Limits

    You cannot enable binary logging by modifying the table properties of an existing table. You must create a table.

-   Example

    When you create a table, you can set the table properties binlog.level and binlog.ttl to enable binary logging. For more information about the parameters that you can use to create a table, see [CREATE TABLE](/intl.en-US/Hologres SQL/DDL/TABLE/CREATE TABLE.md).

    ```
    begin;
    create table test_message_src(
      id int primary key, 
      title text not null, 
      body text);
    call set_table_property('test_message_src', 'orientation', 'row'); // Create a row-oriented table named test_message_src.
    call set_table_property('test_message_src', 'clustering_key', 'id'); // Create a clustered index for the id column.
    call set_table_property('test_message_src', 'binlog.level', 'replica'); // Set the binlog.level property to enable binary logging.
    call set_table_property('test_message_src', 'binlog.ttl', '86400'); // Use the binlog.ttl property to set the time to live (TTL) of binlogs. Unit: seconds.
    commit;
    ```


## Binlog format

Fields in binlogs consist of binlog system fields and user-defined table fields. The following table describes specific fields.

|Field|Data type|Description|
|-----|---------|-----------|
|hg\_binlog\_lsn|BIGINT|The ordinal number of the current binlog. This field is a binlog system field. For binlogs in the same shard, the values of this field monotonically increase and may be discontinuous. For binlogs in different shards, the values of this field may be recurrent and out of order.|
|hg\_binlog\_event\_type|BIGINT|The type of operation in the current record. This field is a binlog system field. This field has four valid values:-   INSERT=5, which indicates that a row or column is inserted.
-   DELETE=2, which indicates that a row or column is deleted.
-   BEFORE\_UPDATE=3, which indicates that a row or column is saved before it is updated.
-   AFTER\_UPDATE=7, which indicates that a row or column is saved after it is updated.

**Note:** An UPDATE operation generates two binlog records, which indicate that the row or column is saved before and after the update. Binary logging ensures that the ordinal numbers of the two records are continuous and in the right order. The record that is generated after the update comes right after the record that is generated before the update. |
|hg\_binlog\_timestamp\_us|BIGINT|The timestamp of the system. Unit: microseconds. This field is a binlog system field.|
|user\_table|User-defined|One or more user-defined table fields. The order of user-defined fields is consistent with the order that is defined in a data definition language \(DDL\) statement. For more information about DDL fields, see [CREATE TABLE](/intl.en-US/Hologres SQL/DDL/TABLE/CREATE TABLE.md).|

## Binary logging for Realtime Compute

Realtime Compute V3.7 and later allow Hologres connectors to consume binlogs in real time. To enable binary logging, perform the following steps:

1.  Use a DDL statement to create a source table.
    -   Syntax

        ```
        create table test_message_src_binlog_table(
          hg_binlog_lsn BIGINT,
          hg_binlog_event_type BIGINT,
          hg_binlog_timestamp_us BIGINT,
          id INTEGER,
          title VARCHAR,
          body VARCHAR
        ) with (
          type = 'hologres',
          `endpoint` = 'ip:port', // The virtual private cloud (VPC) endpoint of your Hologres instance.
          `username` = 'xxxx', // The AccessKey ID of your Alibaba Cloud account.
          `password` = 'xxxx', // The AccessKey secret of your Alibaba Cloud account.
          `dbname` = 'xxxx', // The name of the Hologres database that you want to read.
          `tablename` = 'xxxx', // The name of the Hologres table that you want to read.
          `binlog` = 'true',
          `binlogMaxRetryTimes` = '10',
          `binlogRetryIntervalMs` = '500',
          `binlogBatchReadSize` = '256'
        );
        ```

    -   Parameter description

        In the sample syntax, the three fields with the prefix hg\_binlog\_ are binlog system fields. You cannot modify their names or change their data types. Some of the remaining fields are user-defined fields. The names of user-defined fields must be in lower case. The following table describes more parameters.

        |Parameter|Required|Description|
        |---------|--------|-----------|
        |type|Yes|The type of the source table. Set the value to hologres.|
        |ip:port|Yes|The VPC endpoint of your Hologres instance. You can obtain the VPC endpoint and port information from the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |username|Yes|The AccessKey ID of your Alibaba Cloud account. You can obtain the AccessKey ID from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.|
        |password|Yes|The AccessKey secret of your Alibaba Cloud account. You can obtain the AccessKey secret from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.|
        |dbname|Yes|The name of the Hologres database that you want to read.|
        |tablename|Yes|The name of the Hologres table that you want to read.|
        |binlog|Yes|Specifies whether the current table serves as a source for binlogs. If so, set this parameter to true.|
        |binlogMaxRetryTimes|No|The number of retries after an error occurs in the process of obtaining binlogs. Default value: 60.|
        |binlogRetryIntervalMs|No|The interval between retries after an error occurs in the process of obtaining binlogs. Unit: milliseconds. Default value: 2000.|
        |binlogBatchReadSize|No|The number of binlogs that are obtained at a time. Default value: 16.|

2.  Set a concurrency for binlog subscription.

    The concurrency of binlog subscription equals the number of shards in the Hologres table. You can execute a statement to query the number of shards, as shown in the following code snippet. Replace `$table` in the statement with the name of your table. When you set a concurrency for binlog subscription, we recommend that you set the concurrency to the number of shards in the relevant Hologres table.

    ```
    select tg.property_value from hologres.hg_table_properties tb join hologres.hg_table_group_properties tg on tb.property_value = tg.tablegroup_name where tb.property_key = 'table_group' and tg.property_key = 'shard_count' and table_name = '$table';
    ```


