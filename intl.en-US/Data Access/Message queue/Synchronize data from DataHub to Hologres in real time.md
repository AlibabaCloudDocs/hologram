---
keyword: [Synchronize data, DataHub, Hologres]
---

# Synchronize data from DataHub to Hologres in real time

This topic describes how to use a connector to synchronize data from a DataHub topic to a Hologres table in real time.

-   A Hologres instance is purchased. A development tool is connected to the instance. For more information, see [Use Hologres from the PostgreSQL client](/intl.en-US/Quick Start/Use Hologres from the PostgreSQL client.md).
-   DataHub is activated. For more information, see Get started with DataHub.
-   The following table lists the mappings between concepts in DataHub and Hologres.

    |DataHub|Hologres|
    |-------|--------|
    |Project|Database|
    |Topic|Table|


DataHub is a real-time data distribution platform designed to process streaming data. You can publish and subscribe to streaming data in DataHub and distribute the data to other platforms. This allows you to analyze streaming data and build applications based on the streaming data.

Hologres is a real-time interactive analytics service developed by Alibaba Cloud. It is fully compatible with PostgreSQL and integrates seamlessly with the big data ecosystem. You can use Hologres to analyze and process petabytes of data with high concurrency and low latency.

You can use a connector to synchronize data from a DataHub topic to a Hologres table in real time. Then, you can gain an analytical insight into the data from multiple dimensions and process the data in real time in Hologres.

## Procedure

1.  Create and write data to a data source in DataHub.

    To prepare a data source in DataHub, perform the following steps:

    1.  Create a DataHub project.

        1.  Log on to the [DataHub console](https://dhsnext.console.aliyun.com/cn-hangzhou/projects?spm=5176.cndatahub.0.0.677af05eXclMic). In the left-side navigation pane, click **Project Manager**.
        2.  On the **Project List** page, click **Create Project** in the upper-right corner.
        3.  In the **Create Project** dialog box, set the parameters as required and click **Confirm**.
        ![a](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6498048951/p129088.png)

    2.  Create a DataHub topic.

        1.  On the **Project List** page, find the created project and click the project name.
        2.  On the project details page that appears, click **Create Topic** in the upper-right corner. In the **Create Topic** dialog box, set the parameters as required.
        ![Create Topic](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6498048951/p129089.png)

        **Note:** You can only synchronize data of the TUPLE type, namely structured data, from a DataHub topic to a Hologres table.

        The following table lists the fields in a sample topic.

        |Index|Field|Data type|NULL value allowed|
        |-----|-----|---------|------------------|
        |0|l\_orderkey|BIGINT|False|
        |1|l\_partkey|BIGINT|False|
        |2|l\_suppkey|BIGINT|False|
        |3|l\_linenumber|BIGINT|False|
        |4|l\_quantity|DECIMAL|True|
        |5|l\_extendedprice|DECIMAL|True|
        |6|l\_discount|DECIMAL|True|
        |7|l\_tax|DECIMAL|True|
        |8|l\_returnflag|STRING|True|
        |9|l\_linestatus|STRING|True|
        |10|l\_shipdate|TIMESTAMP|True|
        |11|l\_commitdate|TIMESTAMP|True|
        |12|l\_receiptdate|TIMESTAMP|True|
        |13|l\_shipinstruct|STRING|True|
        |14|l\_shipmode|STRING|True|
        |15|l\_comment|STRING|True|

    3.  Write data to the topic.

        Use a service, such as Blink, or an application to write data to the created topic. The following figure shows the data stored in a sample topic.

        ![Write data](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3516821061/p129091.png)

2.  Create a Hologres table to which the data is synchronized.

    Create a Hologres table to which the data is synchronized. Note that you must create fields with the same names and data types as those of the DataHub topic. For example, you can execute the following statements to create a table:

    ```
    BEGIN;
    CREATE TABLE lineitem ( 
    L_ORDERKEY BIGINT NOT NULL,
    L_PARTKEY BIGINT NOT NULL,
    L_SUPPKEY BIGINT NOT NULL,
    L_LINENUMBER BIGINT NOT NULL,
    L_QUANTITY DECIMAL(20,10),
    L_EXTENDEDPRICE DECIMAL(20,10),
    L_DISCOUNT DECIMAL(20,10),
    L_TAX DECIMAL(20,10),
    L_RETURNFLAG TEXT,
    L_LINESTATUS TEXT,
    L_SHIPDATE TIMESTAMPTZ,
    L_COMMITDATE TIMESTAMPTZ,
    L_RECEIPTDATE TIMESTAMPTZ,
    L_SHIPINSTRUCT TEXT,
    L_SHIPMODE TEXT,
    L_COMMENT TEXT
    );
    
    CALL set_table_property('lineitem', 'orientation', 'column');
    CALL set_table_property('lineitem', 'shard_count', '8');
    
    COMMIT;
    ```

3.  Create a connector to connect DataHub to Hologres.

    1.  In the Topic List section of the project details page, find the created topic and click the topic name.

    2.  On the topic details page that appears, click **+ Connector**.

    3.  In the **Create connector** dialog box, click **Hologres**. In the **Create connector** dialog box, set the parameters as required and click **Create**.

        ![connector](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3516821061/p129426.png)

        |Parameter|Description|Remarks|
        |---------|-----------|-------|
        |Instance|The ID of the Hologres instance.|You can view the ID in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Project|The name of the database in the Hologres instance.|None|
        |Topic|The name of the Hologres table to which data is synchronized.|None|
        |Import Fields|The fields to be synchronized to the Hologres table.|You can synchronize all the fields of the DataHub topic or part of them based on your business requirements.|
        |Auth Mode|The mode in which the access to the Hologres table is authenticated. Default value: AK.|None|
        |AccessId|The AccessKey ID of the current Alibaba Cloud account used to access the Hologres instance.|You can obtain the AccessKey ID in the [User Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) console.|
        |AccessKey|The AccessKey secret of the current Alibaba Cloud account used to access the Hologres instance.|You can obtain the AccessKey secret in the [User Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) console.|

4.  Wait until the data is synchronized to Hologres.

    On the topic details page, click the **Connector** tab. Find the created connector and view the status of the connector. The status indicates the progress of the data synchronization.

    ![chakan](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4516821061/p129356.png)

5.  Query the data in Hologres.

    Connect the Hologres instance to a certain development tool and use the tool to check whether the data is synchronized to the Hologres instance in real time. For more information about development tools, see [Overview](/intl.en-US/Common Development Tools/Overview.md). For example, you can execute the following statement to query the data:

    ```
    SELECT COUNT(*) FROM lineitem;
    ```


## Mappings of data types

The following table lists the mappings between data types in DataHub and Hologres.

|DataHub|Hologres|
|-------|--------|
|BIGINT|BIGINT|
|STRING|TEXT|
|BOOLEAN|BOOLEAN|
|DOUBLE|DOUBLE PRECISION|
|TIMESTAMP|TIMESTAMPTZ|
|DECIMAL|DECIMAL|

