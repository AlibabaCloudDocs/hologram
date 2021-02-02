---
keyword: [create a Hologres source table, write data to Hologres in real time]
---

# Hologres source table

In the original product line, a Blink exclusive cluster is an independent computing cluster that is created on an Alibaba Cloud Elastic Compute Service \(ECS\) instance. This topic describes how to create a Hologres source table by using Blink-based Realtime Compute for Apache Flink in exclusive mode.

## Usage notes

Data is written to a Hologres source table in batch mode. In this case, all data in the table is scanned only once. After the scan is complete, data that is newly imported cannot be written to the Hologres source table.

## Limits

Hologres allows you to use Holo-blink connectors to read data from a Hologres source table and join dimension tables. The Holo-blink connectors have the following limits:

-   By default, the Holo-blink connectors can read only data of tables that use the row-oriented storage type. To obtain data from tables that use the column-oriented storage type, you must add `bulkread='true'` to the code.
-   When you create a table based on the row-oriented storage type, you must set a primary key and set the property of the primary key to clustering key. To create a Hologres source table, use the following sample statement:

    ```
    begin;
    create table test(a int primary key, b text, c text[], d float8, e int8);
    call set_table_property('test', 'orientation', 'row');
    call set_table_property('test', 'clustering_key', 'a');
    commit;
    ```


## DDL syntax

-   Hologres connections are supported in Blink V3.6 and later. To create a Hologres source table, use the following sample data definition language \(DDL\) statement:

    ```
    CREATE TABLE holo_dim_table 
    (
        pk VARCHAR
        ,seller_id VARCHAR
        ,seller_bc_type VARCHAR
        ,seller_tag  VARCHAR
        ,PRIMARY KEY (pk)
    ) with (
        type = 'hologres',
        `endpoint` = '<yourEndpoint>',  // The virtual private cloud (VPC) endpoint of your Hologres instance.
        `userName` = '<yourUsername>',  // The AccessKey ID of your Alibaba Cloud account.
        `password` = '<yourPassword>',  // The AccessKey secret of your Alibaba Cloud account.
        `dbName` = '<yourDbname>',  // The name of the Hologres database to be connected to.
        `tableName` = '<yourTablename>',  // The name of the Hologres table to which data is to be written.
        `bulkRead`='true'
    );
    ```

-   Hologres connections are not supported in Blink versions earlier than V3.6. You must use a Hologres connector and reference relevant JAR files to create a Hologres source table. To obtain relevant JAR files, you can [submit a ticket](https://workorder-intl.console.aliyun.com/) or contact Alibaba Cloud technical support. Then, create a Hologres source table by using the following sample statement:

    **Note:** Blink versions earlier than V3.6 are incompatible with Hologres. We recommend that you update the Blink exclusive cluster to Blink V3.6 or later.

    ```
    CREATE TABLE holo_dim_table 
    (
        pk VARCHAR
        ,seller_id VARCHAR
        ,seller_bc_type VARCHAR
        ,seller_tag  VARCHAR
        ,PRIMARY KEY (pk)
    ) with (
        type = 'custom',
        tableFactoryClass = 'com.alibaba.blink.connectors.hologres.table.factory.HologresTableFactory',
       `endpoint` = '<yourEndpoint>',  // The VPC endpoint of your Hologres instance.
        `userName` = '<yourUsername>', // The AccessKey ID of your Alibaba Cloud account.
        `password` = '<yourPassword>', // The AccessKey secret of your Alibaba Cloud account.
        `dbName` = '<yourDbname>', // The name of the Hologres database to be connected to.
        `tableName` = '<yourTablename>', // The name of the Hologres table to which data is to be written.
        `bulkRead`='true'
    );
    ```


The following table describes the parameters contained in the with object.

|Parameter|Description|Required|
|---------|-----------|--------|
|type|The type of the data source.For Blink V3.6 and later, set the value to hologres. For Blink versions earlier than V3.6, set the value to customer.

|Yes|
|dbname|The name of the Hologres database to be connected to.|Yes|
|tablename|The name of the Hologres table to which data is to be written.|Yes|
|username|The AccessKey ID of your Alibaba Cloud account.You can obtain the AccessKey ID on the [AccessKey Management](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb) page.

|Yes|
|password|The AccessKey secret of your Alibaba Cloud account.You can obtain the AccessKey secret on the [AccessKey Management](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb) page.

|Yes|
|endpoint|The VPC endpoint of your Hologres instance.You can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance), go to the details page of the instance that you want to view, and then find the endpoint on the **Configurations** tab.

|Yes|
|bulkread|Valid values:-   true: indicates that Hologres tables created based on the column-oriented storage type can be used as source tables.
-   false: indicates that only Hologres tables created based on the row-oriented storage type can be used as source tables.

**Note:** If you need to use Hologres tables that are created based on the column-oriented storage type as source tables, set this parameter to true.

|Yes|

## Mappings between data types

For more information about the mappings between data types in Blink and Hologres, see [t1345987.dita\#concept\_1597919](/intl.en-US/Hologres SQL/Data types/Data types.md).

