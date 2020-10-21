---
keyword: [synchronize MaxCompute data, HoloStudio]
---

# Synchronize MaxCompute data

This topic describes how to use HoloStudio to synchronize MaxCompute data to Hologres.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased and bound to a DataWorks workspace. For more information, see [Quick start to HoloStudio](/intl.en-US/HoloStudio/Quick start to HoloStudio.md).

The Data Analytics module of HoloStudio provides the **One-click MaxCompute data synchronization** submodule. In this submodule, you can synchronize MaxCompute data and query the synchronized data in a visualized manner. This provides better performance than creating foreign tables for data queries.

You can also use SQL statements to synchronize MaxCompute data. For more information, see [t1644102.md\#](/intl.en-US/Data Access/Big Data/MaxCompute/Use SQL statements to import MaxCompute to Hologres.md).

1.  Create a data sync node.

    1.  Go to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

    2.  In the top navigation bar of the console, move the pointer over the ![Triangle](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4259623061/p134229.png) icon and select the region where your Hologres instance resides.

    3.  In the left-side navigation pane, click **Instances**. On the **Hologres Instances** page, click **Go to DataWorks - HoloStudio**.

    4.  On the **HoloStudio** page, click the ![Data Analytics](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4259623061/p134237.png) icon in the left-side navigation pane.

    5.  On the **Data Analytics** page, move the pointer over the ![Create](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4259623061/p134243.png) icon.

    6.  Click **One-click MaxCompute data synchronization**.

    7.  In the **Create Node** dialog box, set **Node Name**, **Location**, and **Database**.

    8.  Click **Commit**.

2.  On the editing page of the created node, set the parameters as required.

    ![Set parameters](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9589623061/p134452.png)

    |Parameter|Description|
    |---------|-----------|
    |External table source|    -   **External table already exists**: specifies to use an existing foreign table mapping the MaxCompute table.
    -   **New external table**: specifies to create a foreign table mapping the MaxCompute table. If no existing foreign table that maps the MaxCompute table is available, select **New external table**. |
    |External table table name|The name of the existing foreign table. This parameter is available only when you set External table source to External table already exists.The foreign table must map the MaxCompute table from which data is to be synchronized. |
    |Target Library|The name of the Hologres database to which the MaxCompute data is to be synchronized.|
    |Target schema|The name of the schema in the specified Hologres database.If you have not created a schema, select the default **public** schema. If you have created a custom schema, you can select the created schema. |
    |Destination Table Name|The name of the table to which the MaxCompute data is to be synchronized.|
    |Target table description|The description of the table to which the MaxCompute data is to be synchronized.|
    |Synchronization field|The fields to be synchronized from the specified MaxCompute table.You can select some or all fields in the MaxCompute table. |
    |Partition configuration|The partition fields to be synchronized.The **One-click MaxCompute data synchronization** submodule supports a maximum of one level of partitions. To synchronize a table that contains multiple levels of partitions, execute SQL statements in the **SQL console**. For more information, see [SQL Console](/intl.en-US/HoloStudio/SQL Console.md). |
    |Index configuration|The index to be built for the table to which the MaxCompute data is to be synchronized. For more information, see [Set table properties](/intl.en-US/Hologres SQL/DDL/TABLE/CREATE TABLE.md).|
    |SQL Script|The SQL statements that are executed when the data sync node is run.|

3.  On the editing page of the created node, click the ![Save](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4259623061/p134275.png) icon to save the configuration.

4.  On the editing page of the created node, click the ![Run](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4259623061/p134276.png) icon to synchronize data from the specified MaxCompute table.

5.  On the **PG management** page, view the synchronized data. For more information, see [Manage foreign tables](/intl.en-US/HoloStudio/PG Management/Manage foreign tables.md) or [SQL Console](/intl.en-US/HoloStudio/SQL Console.md).


