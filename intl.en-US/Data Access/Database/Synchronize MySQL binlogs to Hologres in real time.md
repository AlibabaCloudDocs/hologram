---
keyword: [MySQL binlog, data synchronization, Hologres]
---

# Synchronize MySQL binlogs to Hologres in real time

This topic describes how to use the Data Integration module of DataWorks to synchronize MySQL binlogs to Hologres in real time.

-   DataWorks is activated. For more information, see [Overview]().
-   ApsaraDB RDS for MySQL is activated. For more information, see [Quick Start](/intl.en-US/Quick Start/Quick Start.md).

**Note:** Make sure that you activate the DataWorks, Hologres, and ApsaraDB RDS for MySQL services in the same region.

Hologres is a real-time interactive analytics engine that integrates seamlessly with the big data ecosystem. It integrates with the intelligent R&D platform DataWorks to support data query and analysis with high concurrency and low latency.

You can use the MySQL binlog reader to read data from tables in your MySQL database in real time by subscribing to real-time binlogs. Then, you can use the Hologres writer to write MySQL binlogs to Hologres. For more information, see [Hologres Writer]().

The MySQL binlog reader supports the following versions: V5.1, V5.5, V5.6, V5.7, and V8.0.

## Procedure

1.  Prepare a MySQL data store.

    Create a table in an ApsaraDB RDS for MySQL instance and import data to the table. For more information, see [Quick Start](/intl.en-US/Quick Start/Quick Start.md).

    In this example, an existing table in the ApsaraDB RDS for MySQL instance is used.

    You can use the following sample SQL statement to create a table:

    ```
    CREATE TABLE `holo_test` (
      `id` int NULL, 
      `name` text NULL, 
      `r_time` timestamp NULL, 
      `address` text NULL, 
      `salary` double NULL ) 
      ENGINE=InnoDB DEFAULT CHARACTER SET=utf8;
    ```

2.  Configure a connection to the created MySQL data store.

    1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console). In the left-side navigation pane, click **Workspaces**.

    2.  On the Workspaces page, click **Data Integration** in the **Actions** column of the target workspace.

    3.  On the **Data Integration** page, click **Connection**.

    4.  On the page that appears, click **New data source** in the upper-right corner.

    5.  In the Add data source dialog box, click **MySQL Binlog** in the **Realtime** section. In the **Add MySQL Binlog data source** dialog box, set parameters as required.

        ![new](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9964827951/p129499.png)

        |Parameter|Description|
        |---------|-----------|
        |Data source type|The type of the connection. Set the value to **ApsaraDB for RDS**.|
        |Data Source Name|The name of the connection. The name can contain letters, digits, and underscores \(\_\) and must start with a letter.|
        |Data source description|The description of the connection. The description cannot exceed 80 characters in length.|
        |Environment|The environment to which the connection applies. Valid values: **Development** and **Production**. **Note:** This parameter is available only when the workspace is in the standard mode. |
        |Region|The region of the ApsaraDB RDS for MySQL instance.|
        |RDS instance ID|The ID of the ApsaraDB RDS for MySQL instance. You can view the ID in the ApsaraDB for RDS console.|
        |RDS instance account ID|The ID of the Alibaba Cloud account used to purchase the ApsaraDB RDS for MySQL instance. To obtain your Alibaba Cloud account ID, perform the following steps: Log on to the [DataWorks console](https://workbench.data.aliyun.com/console), move the pointer over the profile picture in the upper-right corner, and then click **Security Settings**. On the Security Settings page, you can view the ID of your Alibaba Cloud account.|
        |Database name|The name of the database to which the data store belongs.|
        |User name|The username used to log on to the database.|
        |Password|The password used to log on to the database.|

3.  Configure a connection to Hologres.

    After the preceding MySQL connection is added, configure a connection to Hologres on the **Data Integration** page.

    1.  Click **Connection**.

    2.  On the page that appears, click **New data source** in the upper-right corner.

    3.  In the Add data source dialog box, click **Hologres**. In the **Add Hologres data source** dialog box, set parameters as required.

        ![Add Hologres data source](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6726821061/p129514.png)

        |Parameter|Description|
        |---------|-----------|
        |Data source type|The type of the connection. Valid value: **Alibaba Cloud instance mode**.|
        |Data Source Name|The name of the connection. The name can contain letters, digits, and underscores \(\_\) and must start with a letter.|
        |Data source description|The description of the connection. The description cannot exceed 80 characters in length.|
        |Environment|The environment to which the connection applies. Valid values: **Development** and **Production**. **Note:** This parameter is available only when the workspace is in the standard mode. |
        |Instance ID|The ID of the Hologres instance to which you want to synchronize data. You can view the ID in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Database Name|The name of the database to which the data store belongs.|
        |AccessKey ID|The AccessKey ID of the current Alibaba Cloud account. You can obtain the AccessKey ID in the [User Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) console.|
        |AccessKey Secret|The AccessKey secret of the current Alibaba Cloud account. You can obtain the AccessKey secret in the [User Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) console.|

4.  Configure a real-time sync node.

    After the preceding connections are configured, create a real-time sync node to synchronize data from the MySQL data store to Hologres. To configure a real-time sync node, perform the following steps:

    1.  Create a real-time sync node.

        1.  On the **Data Integration** page, click **New Task**.
        2.  In the **Create Node** dialog box, set **Node Type**, **Node Name**, and **Location**.
        3.  Click **Commit**.
    2.  Configure a reader.

        1.  On the configuration tab of the real-time sync node, drag **MySQL Binlog** in the **Input** section to the editing panel.
        2.  Click the **MySQL Binlog\_1** node. In the pane that appears on the right, set the required parameters in the **Node configuration** section.

            |Parameter|Description|
            |---------|-----------|
            |**Data source**|The connection to the MySQL data store. Select the configured MySQL connection.|
            |**Table**|The name of the table from which data is read in the current data store. You can click **Preview** on the right to preview the selected table.|
            |**Output Fields**|The fields from which data is read.|

    3.  Configure a writer.

        1.  Drag **Hologres** in the **Output** section to the editing panel.
        2.  Click the **Hologres\_1** node. In the pane that appears on the right, set the required parameters in the **Node configuration** section.

            |Parameter|Description|
            |---------|-----------|
            |Data source|The connection to Hologres.|
            |Table|The name of the Hologres table for receiving data. You can click **One-Click table creation** on the right to create a table, or click **Preview** to preview the selected table. |
            |Job Type|The type of the job. Valid values: **Replay \(replay operation log to restore data\)** and **Insert \(direct archive save\)**.             -   **Replay \(replay operation log to restore data\)**: indicates that the Hologres writer performs the same operation on the Hologres data store as that performed on the source data store.
            -   **Insert \(direct archive save\)**: indicates that the Hologres writer uses the Hologres data store as streaming data storage. Data is synchronized from the source data store to the Hologres data store by using the INSERT statement. |
            |Conflict Mode|The solution to data write conflicts. Valid values: **Cover \(Overwrite\)** and **Ignore \(Ignore\)**            -   **Cover \(Overwrite\)**: indicates that the Hologres writer uses the new data synchronized from the source data store to overwrite the existing data in the Hologres data store.
            -   **Ignore \(Ignore\)**: indicates that the Hologres writer ignores the new data synchronized from the source data store and retains the existing data in the Hologres data store. |
            |Field Mapping|The field mappings between the source and destination data stores. Click **Mappings** and set field mappings. The sync node synchronizes data based on the field mappings.|

    4.  Configure basic settings.

        After you configure the reader and writer, click **Basic Configuration** in the right-side navigation pane and specify a resource group for the real-time sync node.

        **Note:** Real-time sync nodes can only be run on **exclusive resource groups** for data integration.

    5.  Commit the real-time sync node.

        1.  After you complete the preceding configurations, click the ![Submit icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0881659951/p98393.png) icon in the toolbar.
        2.  In the **Commit** dialog box that appears, select the component nodes to be committed, set **Description**, and then select **Ignore I/O Inconsistency Alerts**.
        3.  Click **Commit**.

            In a workspace in the standard mode, you need to click **Publish** in the upper-right corner after you commit the real-time sync node. For more information, see [Deploy a node]().

    6.  Start the real-time sync node.

        1.  After a real-time sync node is committed or published, click **Operation Center** in the upper-right corner.
        2.  On the page that appears, choose **RealTime Task Maintenance** \> **RealTime DI** in the left-side navigation pane.
        3.  On the **RealTime DI** page, find the target node and click **Start** in the **Actions** column to start the node.

            You can also click **Reset Start Offset** to set the next startup time for the node.

5.  Query data in Hologres.

    After the real-time sync node is started, you can query the MySQL data in HoloStudio in real time by executing a statement similar to the following example:

    ```
    SELECT COUNT (*)FROM public.holo_test_2;
    ```


## Data stores supported by real-time sync nodes

Real-time sync nodes of DataWorks support multiple disparate data stores. For more information, see [Supported data stores for real-time synchronization]().

