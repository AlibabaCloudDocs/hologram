---
keyword: [create a Hologres source table, write data to Hologres in real time]
---

# Hologres source table

This topic shows you how to create a Hologres source table by using Blink in exclusive mode.

## Usage notes

By default, data is written to a Hologres source table in batch mode. In this case, all the data in the table is scanned only once. After the scan is complete, the data that is newly imported cannot be written to the Hologres source table. If you need to use data in real time in Hologres, see [Subscribe to Hologres binlogs \(Beta\)](/intl.en-US/Data Access/Real-time data writing/Realtime Compute/Blink exclusive/Subscribe to Hologres binlogs (Beta).md).

## Limits

Hologres allows you to use Holo-blink connectors to read data from Hologres source tables and join dimension tables. Holo-blink connectors have the following limits:

-   By default, Holo-blink connectors can read data only from row-oriented tables. To obtain data from column-oriented tables, you must add `bulkread='true'` to the code.
-   When you create a row-oriented table that has a primary key, you must set the primary key as the clustering key of the table. For example, you can execute the following statements to create a Hologres source table:

    ```
    begin;
    create table test(a int primary key, b text, c text[], d float8, e int8);
    call set_table_property('test', 'orientation', 'row');
    call set_table_property('test', 'clustering_key', 'a');
    commit;
    ```

-   Hologres connections are not supported in Blink earlier than V3.6. You must use a Holo-blink connector and reference relevant JAR files to create a Hologres source table. To obtain relevant JAR files, you can [提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)[submit a ticket](https://workorder-intl.console.aliyun.com/) or contact Hologres technical support. Then, execute relevant statements to create a Hologres source table.

## DDL syntax

-   Hologres connections are supported in Blink V3.6 and later and you can reference a Hologres connection when you create a Realtime Compute job. For example, you can configure the following statements for a Realtime Compute job:

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
        `endpoint` = '<yourEndpoint>',  --The virtual private cloud (VPC) endpoint of your Hologres instance.
        `username` = '<yourUsername>',  --The AccessKey ID of your Alibaba Cloud account.
        `password` = '<yourPassword>',  --The AccessKey secret of your Alibaba Cloud account.
        `dbname` = '<yourDbname>',  --The name of the Hologres database to be connected to.
        `tablename` = '<yourTablename>',  --The name of the Hologres table to which you want to write data.
        `bulkread`='true'
    );
    ```

-   Hologres connections are not supported in Blink earlier than V3.6. You must use a Holo-blink connector and reference relevant JAR files to create a Hologres source table. To obtain relevant JAR files, you can [提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)[submit a ticket](https://workorder-intl.console.aliyun.com/) or contact Hologres technical support. Then, execute the following statements to create a Hologres source table:

    **Note:** Blink versions earlier than V3.6 are incompatible with Hologres. We recommend that you update your Blink exclusive cluster to Blink V3.6 or later.

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
       `endpoint` = '<yourEndpoint>',  --The VPC endpoint of your Hologres instance.
        `username` = '<yourUsername>', --The AccessKey ID of your Alibaba Cloud account.
        `password` = '<yourPassword>', --The AccessKey secret of your Alibaba Cloud account.
        `dbname` = '<yourDbname>', --The name of the Hologres database to be connected to.
        `tablename` = '<yourTablename>', --The name of the Hologres table to which you want to write data.
        `bulkread`='true'
    );
    ```


The following table describes the parameters in the with object.

|Parameter|Description|Required|
|---------|-----------|--------|
|type|The type of the data source.Default value: hologres.

|Yes|
|dbname|The name of the Hologres database to be connected to.|Yes|
|tablename|The name of the Hologres table to which you want to write data.|Yes|
|username|The AccessKey ID of your Alibaba Cloud account.You can obtain the AccessKey ID from the [AccessKey Management](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb) page.

|Yes|
|password|The AccessKey secret of your Alibaba Cloud account.You can obtain the AccessKey secret from the [AccessKey Management](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb) page.

|Yes|
|endpoint|The VPC endpoint of your Hologres instance.You can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance), go to the details page of the instance that you want to view, and then obtain the endpoint from the **Configurations** tab. The endpoint must contain a port number, in the format of IP address:Port number.

|Yes|
|bulkread|Specifies whether to allow creating column-oriented source tables in Hologres. Valid values:-   true: Allow creating column-oriented source tables in Hologres.
-   false: Do not allow creating column-oriented source tables in Hologres. Hologres source tables must use the row-oriented storage model.

**Note:** If you need to use column-oriented tables as Hologres source tables, set this parameter to true.

|Yes|

## Mappings between data types

For information about the mappings between the data types supported by Blink in exclusive mode and those supported by Hologres, see [t1345987.dita\#concept\_1597919](/intl.en-US/Hologres SQL/Data types/Data types.md).

