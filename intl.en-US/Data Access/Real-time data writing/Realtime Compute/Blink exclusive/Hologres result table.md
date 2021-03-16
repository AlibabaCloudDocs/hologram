---
keyword: [create a Hologres result table, write data to Hologres in real time]
---

# Hologres result table

Hologres is seamlessly integrated with Blink in exclusive mode, which is a service type in the original product line of Realtime Compute for Apache Flink. This way, you can write data to a Hologres result table by using Holo-blink connectors and query the data in real time. This topic shows you how to write data to a Hologres result table by using Blink in exclusive mode.

## Limits

-   Different Blink versions support different development syntax. Check the Blink version that you select before you use Realtime Compute for Apache Flink and refer to the sample code that is provided for the Blink version.
-   Make sure that you activate the Realtime Compute and Hologres services in the same region. Otherwise, the services cannot connect to each other.
-   Hologres connections are not supported in Blink earlier than V3.6. You must use a Holo-blink connector and reference relevant JAR files to create a Hologres result table. To obtain relevant JAR files, you can [提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)[submit a ticket](https://workorder-intl.console.aliyun.com/) or join the Hologres DingTalk group 32314975.

    **Note:** We recommend that you update Blink to V3.6 or later.

-   Blink V3.7 can automatically create partitioned Hologres tables only after you enter `createparttable='true'` in the code. When you use a partitioned table, take note of the following items:
    -   Hologres tables support only list partitioning.
    -   When you create a partitioned table, you must specify the partition fields. The data of the partition fields must be of the TEXT or INT4 type. A partition key value cannot contain hyphens \(-\). For example, the partition key value `2020-12-09` is invalid.
    -   If you set a primary key for a partitioned table, you must include the partition fields in the primary key.
    -   When you create a child partitioned table, you must specify fixed values as partition key values.
    -   When you write data to a child partitioned table, make sure that the specified fields in the source data have values that fall within the partition key values that you specify for the child partitioned table. Otherwise, an error message is returned.
    -   Hologres tables do not support default partitions.
-   If you have set a primary key for a Hologres table to which data is to be written, the default syntax for writing data in real time is not updated based on the primary key. If the new data to be written shares the same primary key value with existing data, the new data is discarded.
-   Hologres asynchronously writes data. Therefore, you must enter `blink.checkpoint.fail_on_checkpoint_error=true` in the code so that a failover is triggered only when a job exception occurs. You do not need to add this configuration to the code when you use Blink V3.7.6 or later.

## DDL syntax

To create a Hologres result table, execute a data definition language \(DDL\) statement that uses the following syntax:

```
create table Hologres_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  type='hologres',
  dbname='<yourDbname>', --The name of the Hologres database to be connected to.
  tablename='<yourTablename>', --The name of the Hologres table to which you want to write data.
  username='<yourUsername>', --The AccessKey ID of your Alibaba Cloud account.
  password='<yourPassword>', --The AccessKey secret of your Alibaba Cloud account.
  endpoint='<yourEndpoint>'); --The virtual private cloud (VPC) endpoint of your Hologres instance.
```

## Parameters in the with object

|Parameter|Description|Example|
|---------|-----------|-------|
|type|The type of the result table. Set the value to hologres.|hologres|
|endpoint|The VPC endpoint of your Hologres instance.

You can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance), go to the details page of the instance that you want to view, and then obtain the endpoint from the **Configurations** tab. The endpoint must contain a port number, in the format of IP address:Port number.

|demo-cn-hangzhou-vpc.hologres.aliyuncs.com:80|
|username|The AccessKey ID of your Alibaba Cloud account. You can obtain the AccessKey ID from the [AccessKey Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.

|xxxxm3FMWaxxxx|
|password|The AccessKey secret of your Alibaba Cloud account. You can obtain the AccessKey secret from the [AccessKey Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.

|xxxxm355fffaxxxx|
|dbname|The name of the Hologres database to be connected to.|Holodb|
|tablename|The name of the Hologres table to which you want to write data.|blink\_test|
|arraydelimiter|The delimiter that you want to use to split the values of a field of the STRING type into arrays. After the values of a field of the STRING type are split into arrays based on the delimiter specified by the field\_delimiter parameter, a Hologres streaming sink imports the arrays to Hologres.Default value: \\u0002.

|\\u0002|
|mutatetype|The mode in which data is to be written. For more information, see [Create a Hologres result table](/intl.en-US/Fully managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create a Hologres result table.md).Default value: insertorignore.

|insertorignore|
|ignoredelete|Specifies whether to skip retract messages. Valid values:-   true: Skip retract messages.
-   false: Do not skip retract messages.

**Note:** This parameter takes effect only when the streaming semantics is used.

Default value: false.

In most cases, retract messages are generated by the Groupby operation in Realtime Compute for Apache Flink. When the retract messages are transferred to Holo-blink connectors, Delete requests are generated.

|false|
|partitionrouter|Specifies whether to write the data to a partitioned table. Valid values:-   true: Write the data to a partitioned table.
-   false: Do not write the data to a partitioned table.

Default value: false.

|false|
|createparttable|Specifies whether to automatically create a partitioned table based on partition key values if you specify that Realtime Compute for Apache Flink writes data to a partitioned table. This feature is available only in Blink V3.7 and later.Default value: false.

**Note:** Exercise caution when you use this parameter. Make sure that partition key values do not contain dirty data. Otherwise, an invalid partitioned table is created.

|false|

**Note:** The arraydelimiter, mutatetype, ignoredelete, partitionrouter, and createparttable parameters are not provided in the sample DDL statement. If you need to use these parameters, set these parameters based on the description in the preceding table.

## Write data to a standard Hologres result table in real time

1.  Create a table in Hologres.

    Create a table that is used to receive data in Hologres. For example, you can execute the following statement to create a table:

    ```
     create table blink_test (a int, b text, c text, d float8, e bigint);
    ```

2.  Create a Realtime Compute job.

    1.  Log on to the [实时计算控制台](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c0j.14094430.1053885.btn5.380276feCFOVgD)[Realtime Compute console](https://account.alibabacloud.com/login/login.htm?oauth_callback=http://stream-ap-southeast-3.console.aliyun.com/).

    2.  Create a Realtime Compute job.

        -   Hologres connections are supported in Blink V3.6 and later and you can reference a Hologres connection when you create a Realtime Compute job. For example, you can configure the following statements for a Realtime Compute job:

            ```
            create table randomSource (a int, b VARCHAR, c VARCHAR, d DOUBLE, e BIGINT) with (type = 'random');
            
            create table test (
              a int,
              b VARCHAR,
              c VARCHAR,
              PRIMARY KEY (a)
            ) with (
              type = 'hologres',
              `endpoint` = '$ip:$port', --The VPC endpoint and port number of your Hologres instance.
              `username` = 'AccessKey ID of your Alibaba Cloud account',
              `password` = 'AccessKey secret of your Alibaba Cloud account',
              `dbname` = 'Name of the Hologres database to be connected to',
              `tablename` = 'blink_test'--The name of the Hologres table to which you want to write data.
            );
            
            insert
              into test
            select
              a,b,c
            from
              randomSource;
            ```

        -   Hologres connections are not supported in Blink earlier than V3.6. You must use a Holo-blink connector and reference relevant JAR files to create a Hologres result table. To obtain relevant JAR files, you can [提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)[submit a ticket](https://workorder-intl.console.aliyun.com/) or join the Hologres DingTalk group 32314975. For example, you can configure the following statements for a Realtime Compute job:

            ```
            create table randomSource (a int, b VARCHAR, c VARCHAR, d DOUBLE, e BIGINT) with (type = 'random');
            create table test (
              a int,
              b VARCHAR,
              c VARCHAR,
              PRIMARY KEY (a)
            ) with (
              type = 'custom',
              tableFactoryClass = 'com.alibaba.blink.connectors.hologres.table.factory.HologresTableFactory',
              `endpoint` = '$ip:$port', --The VPC endpoint and port number of your Hologres instance.
              `username` = 'AccessKey ID of your Alibaba Cloud account',
              `password` = 'AccessKey secret of your Alibaba Cloud account',
              `dbname` = 'Name of the Hologres database to be connected to',
              `tablename` = 'blink_test', // The name of the Hologres table to which you want to write data.
            );
            
            insert
              into test
            select
              a,b,c
            from
              randomSource;
            ```

3.  Commit the job.

    1.  After you compile the job code, click **Syntax Check** in the upper part of the job editor. If the **succeed** message appears, the code syntax is correct.
    2.  Click **Save**.
    3.  Click **Publish**. In the dialog box that appears, set the parameters for committing the job to the production environment as needed.

        ![4](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5908158951/p84223.png)

4.  Run the job.

    After the job is committed to the production environment, manually run the job.

    In the top navigation bar of the **Development Platform** page, click **Administration** in the upper-right corner. On the Administration page, select the job that you committed and click **Start** in the upper-right corner.

    ![6](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6908158951/p84227.png)

5.  Query data in Hologres in real time.

    You can query data written to the Hologres table that is created in Step 1. For example, you can execute the following statement to query the data:

    ```
    select * from blink_test;
    ```


## Merge data into a wide table

You may need to write data from multiple data streams to the same Hologres wide table. Refer to the following example:

Assume that you have a Hologres wide table named WIDE\_TABLE. The table has the following fields: A, B, C, D, and E. Field A is the primary key. In Realtime Compute for Apache Flink, one stream contains data in the A, B, and C fields, and another stream contains data in the A, D, and E fields.

1.  Use Flink SQL to declare two Hologres result tables. For one of the tables, define the A, B, and C fields. For the other table, define the A, D, and E fields. Both tables map to the *WIDE\_TABLE* table.
2.  Set the mutatetype parameter to insertorupdate for both result tables.
3.  Set the ignoredelete parameter to true for both result tables. This prevents retract messages from generating Delete requests.
4.  Insert the data from the two streams into their mapping result tables.

Take note of the following limits:

1.  The wide table must have a primary key.
2.  The data of each stream must contain all the fields in the primary key.
3.  If the wide table is a column-oriented table and the number of requests per second is large, the CPU utilization becomes relatively high. We recommend that you disable dictionary encoding for the fields in the table.

## Write data to a partitioned result table in Hologres in real time

You can call the HoloHub API to write data to a parent partitioned table. Then, the system routes data of different partitions to the corresponding child partitioned tables. Alternatively, you can write data to a partitioned table. For more information about the HoloHub API, see [Overview of the HoloHub API](/intl.en-US/Data Access/Real-time data writing/Realtime Compute/Blink exclusive/Overview of the HoloHub API.md).

Take note of the following limits:

-   Hologres tables support only list partitioning.
-   When you create a partitioned table, you must specify the partition fields. The data of the partition fields must be of the TEXT or INT4 type.
-   If you set a primary key for a partitioned table, you must include the partition fields in the primary key.
-   When you create a child partitioned table, you must specify fixed values as partition key values.
-   When you write data to a child partitioned table, make sure that the specified fields in the source data have values that fall within the partition key values that you specify for the child partitioned table. Otherwise, an error message is returned.
-   Hologres does not support default partitions.

1.  Create partitioned tables in Hologres.

    Create a parent partitioned table that is used to receive data in Hologres and create child partitioned tables that correspond to the partitioned table. For example, you can execute the following statements to create tables:

    ```
    --Create a parent partitioned table named test_message and child partitioned tables that correspond to the parent partitioned table.
    
    drop table if exists test_message;
    
    begin;
    create table test_message (
     "bizdate" text NOT NULL,
     "tag" text NOT NULL,
     "id" int4 NOT NULL,
     "title" text NOT NULL,
     "body" text,
    PRIMARY KEY (bizdate,tag,id)
    )
    PARTITION BY LIST (bizdate);
    commit;
    ```

    **Note:**

    -   When you execute the statements, you must assign a value to the `${bizdate}` parameter.
    -   Only Blink V3.7 can automatically create child partitioned tables. If your Blink version is earlier than V3.7, you must manually create child partitioned tables in Hologres before you import data. Otherwise, the data import fails.
2.  Create a Realtime Compute job when Blink in exclusive mode is used.

    For example, you can configure the following statements for a Realtime Compute job when Blink in exclusive mode is used:

    **Note:** The following sample statements apply to Blink V3.7 and later. If your Blink version is earlier than V3.7, you must update Blink to V3.7 or later, or delete ``createparttable` = 'true'` from the code that is used to automatically create child partitioned tables.

    ```
    create table test_message_src(
      tag VARCHAR,
      id INTEGER,
      title VARCHAR,
      body VARCHAR
    ) with (
      type = 'random',
      `interval` = '10',
      `count` = '100'
    );
    
    create table test_message_sink (
      bizdate VARCHAR,
      tag VARCHAR,
      id INTEGER,
      title VARCHAR,
      body VARCHAR
    ) with (
      type = 'hologres',
      `endpoint` = '$ip:$port', --The VPC endpoint of your Hologres instance.
      `username` ='<AccessID>', --The AccessKey ID of your Alibaba Cloud account.
      `password` = '<AccessKey>', --The AccessKey secret of your Alibaba Cloud account.
      `dbname` = '<DBname>', --The name of the Hologres database to be connected to.
      `tablename` = '<Tablename>', --The name of the Hologres table to which you want to write data.
      `partitionrouter` = 'true', --Write data to a partitioned table in Hologres.
      `createparttable` = 'true', // Automatically create child partitioned tables in Hologres.
    );
    
    insert into test_message_sink select * from test_message_src;
    ```

3.  Commit and run the job.

    For more information, see the Commit the job and Run the job steps in the Write data to a standard Hologres result table in real time section.

4.  Query data in Hologres in real time.

    You can query data written to the parent partitioned table that is created in Step 1. For example, you can execute the following statements to query the data:

    ```
    select * from test_message;
    select * from test_message where bizdate = '20200327';
    ```


## Mappings between data types

For information about the mappings between the data types supported by Blink in exclusive mode and those supported by Hologres, see [t1345987.dita\#concept\_1597919](/intl.en-US/Hologres SQL/Data types/Data types.md).

