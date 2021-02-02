---
keyword: [import MaxCompute data, HoloWeb]
---

# Synchronize MaxCompute data with a few clicks

This topic describes how to use HoloWeb to import data from MaxCompute tables in a visualized manner.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

HoloWeb allows you to synchronize MaxCompute data with a few clicks. You can import data from MaxCompute tables and query the data in a visualized manner in the HoloWeb console. This provides better performance than creating foreign tables for data queries.

1.  Connect the purchased Hologres instance to HoloWeb. For more information, see [Manage a connection](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a connection.md).

2.  On the HoloWeb page, click **Connection Management** in the top navigation bar, move the pointer over **Accelerated Queries of MaxCompute Data**, and then select **Synchronize MaxCompute data**.

3.  On the **Create MaxCompute Data Synchronization Task** page, set the parameters as required.

    ![Create MaxCompute Data Synchronization Task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7204322161/p186629.png)

    The following table describes the parameters.

    |Category|Parameter|Description|
    |--------|---------|-----------|
    |Select Connection|Connection name|The name of the required connection.|
    |Database|The name of the Hologres database.|
    |Select MaxCompute Source Table|Source Table Type|    -   **Existing external table**: specifies to use an existing foreign table that maps the MaxCompute table.
    -   **New external table**: specifies to create a foreign table that is used to map the MaxCompute table. |
    |Source Schema|The schema in which the foreign table that maps the MaxCompute table resides.This parameter is displayed if you select **Existing external table** for the **External table source** parameter. |
    |Foreign table name|The name of the foreign table that maps the MaxCompute table.This parameter is displayed if you select **Existing external table** for the **External table source** parameter. |
    |Server|The server on which the foreign table is stored. You can select the **odps\_server** server that is created at the underlying layer of Hologres. For more information, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k).This parameter is displayed if you select **New external table** for the **External table source** parameter. |
    |Table name|The name of the MaxCompute table, including the name of the MaxCompute project.Format: **project.table\_name**.

This parameter is displayed if you select **New external table** for the **External table source** parameter. |
    |Destination Table|Destination Schema|The name of the schema in which the destination table resides.If you have not created a schema, select the default schema **public**. If you have created a schema, you can select the created schema. |
    |Destination Table Name|The name of the Hologres internal table to which the data in the MaxCompute table is synchronized.|
    |Destination Table Description|The description of the destination table.|
    |Synchronization Settings|Synchronization field|The fields to be synchronized from the MaxCompute table.You can import part of or all fields in the MaxCompute table. |
    |Partition Configuration|Partition Field|If you select a partition field, Hologres automatically creates a partitioned table as the destination table.Hologres supports a maximum of one level of partitioning. When you import data from a MaxCompute table that involves multiple levels of partitioning, you must set only the first-level partition field of the MaxCompute table for the destination table. Other partition fields in the MaxCompute table are mapped to common fields in the destination table. |
    |Data TimeStamp|If a MaxCompute table is partitioned by date, you can specify a date. The system automatically imports data of the specified date to the destination table.|
    |Index Configuration|Storage mode|    -   **Column storage**: This mode is applicable to various complex queries.
    -   **Row deposit**: This mode is applicable to point query and scan based on primary keys.
Default value: **Column storage**. |
    |Life Cycle \(seconds\)|The lifecycle of the table data. Default value: **Permanent**.If the data is not updated within that specified lifecycle, the system automatically deletes the data after the lifecycle ends. |
    |Clustered index|The clustering key that is used to sort columns.The type of index determines the order of fields. If you select the clustered index, Hologres can accelerate range and filter queries on index fields. |
    |Segment Key|The fields that are used to segment data. If the specified fields are involved in the query conditions, Hologres can find the storage location of data based on the fields.|

    In the **SQL Statements** section, SQL statements of the visualized operations are automatically generated in the SQL editor.

4.  Click **Submit** in the upper-right corner.


