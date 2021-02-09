---
keyword: [create a Hologres result table, write data to Hologres in real time]
---

# Hologres result table

Hologres is seamlessly integrated with Blink-based Realtime Compute for Apache Flink in exclusive mode in the original production line. This way, you can write data to a Hologres result table by using Hologres connectors and query data that is written to tables in real time. This topic describes how to write data to a Hologres result table by using Blink-based Realtime Compute for Apache Flink in exclusive mode in the original production line.

## Limits

-   Different Blink versions support different development syntax. Check the Blink version that you select before you use Realtime Compute for Apache Flink and refer to the sample code that is provided for the Blink version.
-   Make sure that you activate the Realtime Compute and Hologres services in the same region. Otherwise, the services cannot access each other.
-   Blink earlier than V3.6 does not support Holorges connectors. To write data to Hologres in real time, you must reference JAR files. To obtain relevant JAR files, you can [submit a ticket](https://workorder-intl.console.aliyun.com/) or join the Hologres DingTalk group 32314975.

    **Note:** We recommend that you update Blink to V3.6 or later.

-   Blink V3.7 can automatically create Hologres partitioned tables only after you enter `createparttable='true'` in the code. When you use a partitioned table, take of the following items:
    -   Hologres tables support only list partitioning.
    -   When you create a partitioned table, you must specify the partition fields. The data of the partition fields must be of the TEXT or INT4 types. A partition key value cannot contain hyphens \(-\). For example, the partition key value `2020-12-09` is invalid.
    -   If you set a primary key for a partitioned table, you must include the partition fields in the primary key.
    -   When you create a child partitioned table, you must set its partition fields to fixed values.
    -   When you write data to a child partitioned table, make sure that the source partitions of the data are the same as the partitions you specify for the child partitioned table. Otherwise, an error message is returned.
    -   Hologres tables do not support default partitions.
-   If you have set a primary key for a Hologres table to which data is to be written, the default syntax for writing data in real time is not updated based on the primary key. In this case, if data that is newly written to one partition field exists, the data is discarded.
-   Hologres writes data asynchronously. Therefore, you must enter `blink.checkpoint.fail_on_checkpoint_error=true` in the code so that a failover is triggered only when a job exception occurs. You do not need to add this parameter to the code when you use Blink V3.7.6 or later.

## DDL syntax

To create a Hologres result table, execute data definition language \(DDL\) statements that use the following syntax:

```
create table Hologres_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  type='hologres',
  dbname='<yourDbname>', // The name of the Hologres database to be connected to.
  tablename='<yourTablename>', // The name of the Hologres table to which data is to be written.
  username='<yourUsername>', // The AccessKey ID of your Alibaba Cloud account.
  password='<yourPassword>', // The AccessKey secret of your Alibaba Cloud account.
  endpoint='<yourEndpoint>'); // The virtual private cloud (VPC) endpoint of your Hologres instance.
```

## Parameters in the with object

|Parameter|Description|Example|
|---------|-----------|-------|
|type|The type of the result table.**Note:** For Blink V3.6 or later, set the value to hologres. For Blink versions earlier than V3.6, set the value to custom.

|hologres|
|endpoint|The VPC endpoint of your Hologres instance.

You can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance), go to the details page of the instance that you want to view, and obtain the endpoint on the **Configurations** tab.

|demo-cn-hangzhou-vpc.hologres.aliyuncs.com:80|
|username|AccessKey ID You can obtain the AccessKey ID on the [AccessKey Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.

|xxxxm3FMWaxxxx|
|password|AccessKey SecretYou can obtain the AccessKey secret on the [AccessKey Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.

|xxxxm355fffaxxxx|
|dbName|The name of the Hologres database to be connected to.|Holodb|
|tableName|The name of the Hologres table to which data is to be written.|blink\_test|
|arraydelimiter|A Hologres streaming sink allows you to split a field of the STRING type into arrays based on the field\_delimiter parameter. Then, the sink imports the arrays to Hologres.Default value: \\u0002.

|\\u0002|
|mutatetype|The mode in which data is to be written. For more information, see [t1936707.md\#]().Default value: insertorignore.

|insertorignore|
|ignoredelete|Specifies whether to skip retract messages. Valid values:-   true: indicates that retract messages are not skipped.
-   false: indicates that retract messages are not skipped.

**Note:** This parameter takes effect only when the streaming semantics is used.

Default value: false.

In most cases, retract messages are generated by the Groupby operation in Flink. When the retract messages are transferred to Hologres connectors, Delete requests are generated.

|false|
|partitionrouter|Specifies whether to write data to a partitioned table. Valid values:-   true: indicates that data is written to a partitioned table.
-   false: indicates that data is not written to a partitioned table.

Default value: false.

|false|
|createparttable|Specifies whether a partitioned table is to be automatically created based on partition key values if you specify that Realtime Compute for Apache Flink writes data to a partitioned table. This feature is available only in Blink V3.7 and later.Default value: false.

**Note:** Exercise caution when you use this parameter. Make sure that partition key values do not contain dirty data. Otherwise, an invalid partitioned table is created.

|false|

**Note:** The arraydelimiter, mutatetype, ignoredelete, partitionrouter, and createparttable parameters are not shown in the sample DDL statements. If you need to use these parameters, set these parameters based on the description in the preceding table.

## Write data to a standard Hologres result table in real time

1.  Create a table in Hologres.

    Create a table that is used to receive data in Hologres. For example, you can execute the following statement to create a table:

    ```
     create table blink_test (a int, b text, c text, d float8, e bigint);
    ```

2.  Create a Realtime Compute job.

    1.  Log on to the [Realtime Compute console](https://account.alibabacloud.com/login/login.htm?oauth_callback=http://stream-ap-southeast-3.console.aliyun.com/).

    2.  Create a Realtime Compute job.

        -   Hologres connections are supported in Blink V3.6 and later and you can use a Hologres connection to create a Realtime Compute job. For example, you can execute the following statements to create a Realtime Compute job:

            ```
            create table randomSource (a int, b VARCHAR, c VARCHAR, d DOUBLE, e BIGINT) with (type = 'random');
            
            create table test (
              a int,
              b VARCHAR,
              c VARCHAR,
              PRIMARY KEY (a)
            ) with (
              type = 'hologres',
              `endpoint` = '$ip:$port', // The VPC endpoint and port number of your Hologres instance.
              `username` = 'AccessKey ID of your Alibaba Cloud account',
              `password` = 'AccessKey secret of your Alibaba Cloud account',
              `dbName` = 'Name of the Hologres database to be connected to',
              `tableName` = 'blink_test', // The name of the Hologres table to which data is to be written.
            );
            
            insert
              into test
            select
              a,b,c
            from
              randomSource;
            ```

        -   Hologres connections are not supported in Blink versions earlier than V3.6. You must use a Hologres connector to write data and reference relevant JAR files to create a Hologres result table. To obtain relevant JAR files, you can [submit a ticket](https://workorder-intl.console.aliyun.com/) or join the Hologres DingTalk group 32314975. For example, you can execute the following statements to create a Realtime Compute job:

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
              `endpoint` = '$ip:$port', // The VPC endpoint and port number of your Hologres instance.
              `username` = 'AccessKey ID of your Alibaba Cloud account',
              `password` = 'AccessKey secret of your Alibaba Cloud account',
              `dbName` = 'Name of the Hologres database to be connected to',
              `tableName` = 'blink_test', // The name of the Hologres table to which data is to be written.
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
    2.  Click **Save**. Then, the job is saved.
    3.  Click **Publish**. In the dialog box that appears, set parameters for committing the job to the production environment as needed.

        ![4](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5908158951/p84223.png)

4.  Run the job.

    After the job is committed to the production environment, manually run the job.

    In the top navigation bar of the **Development Platform** page, click **Administration** in the upper-right corner. On the Administration page, select the job that you want to run and click **Start** in the upper-right corner.

    ![6](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6908158951/p84227.png)

5.  Query data in Hologres in real time.

    You can query data written to the Hologres table that is created in Step 1. For example, you can execute the following statement to query data:

    ```
    select * from blink_test;
    ```


## Write data to a partitioned result table in Hologres in real time

You can call the HoloHub API to write data to a parent partitioned table. Then, the system routes data of different partitions to the corresponding child partitioned tables. Alternatively, you can write data to a partitioned table. For more information about the HoloHub API, see [Overview of the HoloHub API](/intl.en-US/Data Access/Big Data/Realtime Compute/Blink exclusive/Overview of the HoloHub API.md).

Take note of the following limits:

-   Hologres tables support only list partitioning.
-   When you create a partitioned table, you must specify the partition fields. The data of the partition fields must be of the TEXT or INT4 types.
-   If you set a primary key for a partitioned table, you must include the partition fields in the primary key.
-   When you create a child partitioned table, you must set its partition fields to fixed values.
-   When you write data to a child partitioned table, make sure that the source partitions of the data are the same as the partitions you specify for the child partitioned table. Otherwise, an error message is returned.
-   Hologres does not support default partitions.

1.  Create partitioned tables in Hologres.

    Create a parent partitioned table that is used to receive data in Hologres and create child partitioned tables that correspond to the partitioned table. For example, you can execute the following statements to create tables:

    ```
    // Create a parent partitioned table named test_message and child partitioned tables that correspond to the parent partitioned table.
    
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

    -   You must assign a value to the `${bizdate}` parameter when you execute the statements.
    -   Only Blink V3.7 can automatically create partitioned tables. If your Blink version is earlier than V3.7, you must manually create child partitioned tables in Hologres before you import data. Otherwise, the data import fails.
2.  Create a Realtime Compute job by using Blink in exclusive mode.

    For example, you can execute the following statements to create a Realtime Compute job by using Blink in exclusive mode.

    **Note:** The following sample statements apply to Blink V3.7 and later. If your Blink version is earlier than V3.7, you must upgrade Blink to V3.7 or later, or delete ``createparttable` = 'true'` from the code that is used to automatically create child partitioned tables.

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
      `endpoint` = '$ip:$port', // The VPC endpoint of your Hologres instance.
      `username` ='<AccessID>', // The AccessKey ID of your Alibaba Cloud account.
      `password` = '<AccessKey>', // The AccessKey secret of your Alibaba Cloud account.
      `dbName` = '<DBname>', // The name of the Hologres database to be connected to.
      `tableName` = '<Tablename>', // The name of the Hologres table to which data is to be written.
      `partitionRouter` = 'true', // Write data to a parent partitioned table in Hologres.
      `createparttable` = 'true', // Automatically create child partitioned tables in Hologres
    );
    
    insert into test_message_sink select * from test_message_src;
    ```

3.  Commit and run the job.

    For more information, see the Commit the job and Run the job steps in the Write data to a standard Hologres result table in real time section.

4.  Query data in Hologres in real time.

    You can query data written to the parent partitioned table that is created in Step 1. For example, you can execute the following statements to query data:

    ```
    select * from test_message;
    select * from test_message where bizdate = '20200327';
    ```


## Mappings between data types

For more information about the mappings between data types in Blink and Hologres, see [t1345987.dita\#concept\_1597919](/intl.en-US/Hologres SQL/Data types/Data types.md).

