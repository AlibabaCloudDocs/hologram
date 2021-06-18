---
keyword: [import MaxCompute data, HoloWeb]
---

# Synchronize MaxCompute data with a few clicks

This topic describes how to use HoloWeb to import data from MaxCompute tables in a visualized manner.

You have logged on to a Hologres instance. For more information, see [Log on to an instance](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Log on to an instance.md).

HoloWeb allows you to synchronize MaxCompute data with a few clicks. You can import data from MaxCompute tables and query the data in a visualized manner in the HoloWeb console. This provides better performance than creating foreign tables for data queries.

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  On the **Hologres Instances** page, click **Go to HoloWeb** to go to the HoloWeb console.

4.  In the top navigation bar of the HoloWeb console, choose **Metadata Management** \> **MaxCompute Acceleration** \> **Import MaxCompute Data**.

5.  On the **Create MaxCompute Data Import Task** page, set the parameters as required.

    ![Import MaxCompute Data](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9329393261/p273814.png)

    The following table describes the parameters.

    |Category|Parameter|Description|
    |--------|---------|-----------|
    |Connection Information|Instance Name|The name of the current instance.|
    |Database|The name of the current database.|
    |Select MaxCompute Source Table|Source Table Type|    -   **Existing Foreign Table**: uses an existing foreign table that maps the MaxCompute table.
    -   **New external table**: creates a foreign table that is used to map the MaxCompute table. |
    |Source Schema|The schema in which the foreign table that maps the MaxCompute table resides. This parameter is displayed if you select **Existing Foreign Table** for the **Source Table Type** parameter. |
    |Foreign Table Name|The name of the foreign table that maps the MaxCompute table. This parameter is displayed if you select **Existing Foreign Table** for the **Source Table Type** parameter. |
    |Server|The server on which the foreign table is stored. You can select the **odps\_server** server that is created at the underlying layer of Hologres. For more information, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k). This parameter is displayed if you select **New external table** for the **Source Table Type** parameter. |
    |Table Name|The name of the MaxCompute table, including the name of the MaxCompute project. Format: **project.table\_name**.

This parameter is displayed if you select **New external table** for the **Source Table Type** parameter. |
    |Destination Table|Destination Schema|The name of the schema in which the destination table resides. If you have not created a schema, select the default schema **public**. If you have created a schema, you can select the created schema. |
    |Destination Table Name|The name of the Hologres internal table to which the data in the MaxCompute table is synchronized.|
    |Destination Table Description|The description of the destination table.|
    |Import Task|Field|The fields to be synchronized from the MaxCompute table. You can import part of or all fields in the MaxCompute table. |
    |Partition|Partition Field|If you select a partition field, Hologres automatically creates a partitioned table as the destination table. Hologres supports a maximum of one level of partitioning. When you import data from a MaxCompute table that involves multiple levels of partitioning, you must set only the first-level partition field of the MaxCompute table for the destination table. Other partition fields in the MaxCompute table are mapped to regular fields in the destination table. |
    |Data Timestamp|If a MaxCompute table is partitioned by date, you can specify a date. The system automatically imports data of the specified date to the destination table.|
    |Property|Storage Format|    -   **Column storage**: This mode is applicable to various complex queries.
    -   **Row storage**: This mode is applicable to point query and scan based on primary keys.
Default value: **Column storage**. |
    |Life Cycle \(seconds\)|The lifecycle of the table data. Default value: **Forever**. If the data is not updated within the specified period, the system deletes the data after the period expires. |
    |Clustered Index|The clustering key that is used to sort columns. The type of index determines the order of fields. Hologres can use clustered indexes to accelerate range and filter queries on index fields. |
    |Event Time Column|The fields that are used to segment data. If the specified fields are involved in the query conditions, Hologres can find the storage location of data based on the fields.|
    |Dictionary Encoding|The fields based on whose values a dictionary mapping is built. Dictionary encoding can convert string comparisons to numeric comparisons to accelerate queries such as GROUP BY and FILTER.

By default, the system selects all the fields of the **text** type for this parameter. |
    |Bitmap Columns|The bit fields on which bit codes are built. You can filter the data that meets the query conditions based on the specified fields by using Hologres.

By default, the system selects all the fields of the **text** type for this parameter. |
    |Distribution Column|The distribution key. Hologres shuffles data to each shard based on the specified column. Data entries with the same distribution key value are distributed to the same shard. If you use a distribution key as a filter condition, the execution efficiency can be greatly improved.|

    In the **SQL Statements** section, the SQL statements of the visualized operations are automatically generated in the SQL editor.

6.  Click **Submit** in the upper-right corner.


