---
keyword: [Data Integration, Hologres, ApsaraDB RDS for MySQL]
---

# Use batch sync nodes of DataWorks to synchronize data from ApsaraDB RDS for MySQL to Hologres

This topic describes how to use a batch sync node provided by the Data Integration service of DataWorks to synchronize data from ApsaraDB Relational Database Service \(RDS\) for MySQL to Hologres.

-   DataWorks is activated. For more information, see [Overview]().
-   ApsaraDB RDS for MySQL is activated. For more information, see [Quick Start](/intl.en-US/Quick Start/Quick Start.md).

**Note:** If the preceding services are activated in different regions, check how to synchronize data across regions. For more information, see [Test data store connectivity]().

ApsaraDB for RDS is a stable, reliable, and scalable online database service. It provides a complete database solution that includes disaster recovery, data backup, data recovery, and data migration.

Hologres is a real-time interactive analytics engine. It integrates with the big data ecosystem and DataWorks. You can use batch sync nodes provided by the Data Integration service of DataWorks to synchronize data from ApsaraDB RDS for MySQL to Hologres and use Hologres to query and analyze the synchronized data.

1.  Prepare data in the ApsaraDB RDS for MySQL instance.

    Create a table and import data to the table in the ApsaraDB RDS for MySQL instance. For example, you can use the following SQL statement:

    ```
    CREATE TABLE `rds_test` (
        `id` int(11) NULL COMMENT 'ID',
        `name` text NULL COMMENT 'Name',
        `r_time` datetime NULL COMMENT 'Time',
        `address` text NULL COMMENT 'Address',
        `salary` double NULL COMMENT 'Salary'
    ) ENGINE=InnoDB
    DEFAULT CHARACTER SET=utf8 COLLATE=utf8_general_ci;
    ```

    You can also use an existing table in the ApsaraDB RDS for MySQL instance.

2.  Configure a connection to the Hologres instance.

    1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console?#/).

    2.  In the left-side navigation pane, click **Workspaces**.

    3.  In the top navigation bar, select the region where the target workspace resides. Find the workspace and click **Data Integration** in the Actions column.

    4.  On the Data Integration page, click **Connection** in the left-side navigation pane.

    5.  On the **Data Source** page, click **New data source** in the upper-right corner.

    6.  In the Add data source dialog box, click **Hologres** in the **Big Data Storage** section.

    7.  In the **Add Hologres data source** dialog box, set parameters for the Hologres connection.

        ![Add Hologres data source](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4073560061/p139621.png)

        |Parameter|Description|
        |---------|-----------|
        |Data source type|The type of the connection. Valid value: **Alibaba Cloud instance mode** |
        |Data Source Name|The name of the connection. The name can contain letters, digits, and underscores \(\_\) and must start with a letter.|
        |Description|The description of the connection. The description cannot exceed 80 characters in length.|
        |Applicable environment|The environment where the connection can be used. Valid values:

        -   **Development**
        -   **Production**
**Note:** This parameter is available only when the workspace is in standard mode. |
        |Instance ID|The ID of the Hologres instance to which you want to synchronize data.You can view the ID in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
        |Database Name|The name of the Hologres database to which you want to synchronize data.|
        |AccessKey ID|The AccessKey ID of the current Alibaba Cloud account. You can obtain the AccessKey ID in the [User Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) console.|
        |AccessKey Secret|The AccessKey secret of the current Alibaba Cloud account. You can obtain the AccessKey secret in the [User Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) console.|

        **Note:** Hologres connections support only exclusive resource groups for Data Integration.

3.  Configure a connection to the ApsaraDB RDS for MySQL instance.

    1.  On the **Data Source** page, click **New data source** in the upper-right corner.

    2.  In the Add data source dialog box, click **MySQL** in the **Relational Database** section.

    3.  In the **Add MySQL data source** dialog box, set parameters for the MySQL connection.

        ![mysql](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6755901061/p139801.png)

        |Parameter|Description|
        |---------|-----------|
        |Data source type|Valid values:

        -   **Alibaba Cloud instance mode**
        -   **Connection string mode** |
        |Data Source Name|The name of the connection. The name can contain letters, digits, and underscores \(\_\) and must start with a letter.|
        |Description|The description of the connection. The description cannot exceed 80 characters in length.|
        |Applicable environment|The environment where the connection can be used. Valid values:

        -   **Development**
        -   **Production**
**Note:** This parameter is available only when the workspace is in standard mode. |
        |Region|The region where the ApsaraDB RDS for MySQL instance resides.|
        |RDS instance ID|The ID of the ApsaraDB RDS for MySQL instance from which you want to synchronize data.You can view the ID in the [ApsaraDB for RDS console](https://rdsnext.console.aliyun.com/#/rdsList/cn-shanghai/basic/). |
        |Database name|The name of the ApsaraDB RDS for MySQL database from which you want to synchronize data.|
        |User name|The username used to log on to the ApsaraDB RDS for MySQL database.|
        |Password|The password used to log on to the ApsaraDB RDS for MySQL database.|

4.  Create a batch sync node in the Data Integration service of DataWorks.

    1.  On the DataOS Panel page of the workspace where you want to create a batch sync node, click the ![Icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8421821061/p139848.png) icon in the upper-left corner.

    2.  On the menu that appears, choose All Products \> **Data Integration**.

    3.  On the **Data Integration** page, click **New offline synchronization**.

    4.  In the **New node** dialog box, set **Node type**, **Node name**, and **Destination folder**.

        ![New node](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7755901061/p139854.png)

        If you have not created a workflow, create one before you create a node. For more information, see [Create a workflow]().

5.  Configure the batch sync node.

    1.  On the configuration tab of the batch sync node, set the parameters in the **Select data source** step.

        ![Data source](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7755901061/p140870.png)

        |Section|Parameter|Description|Required|
        |-------|---------|-----------|--------|
        |Data source|Data source|The type and name of the connection from which you want to synchronize data.In this example, a MySQL connection is used.

|Yes|
        |Table|The name of the table from which you want to synchronize data.|Yes|
        |Data Filtering|The filter for data synchronization.Filtering based on the `LIMIT` keyword is not supported.

The SQL syntax varies with the connection type. For more information, see [Scheduling parameters]().

|No|
        |Split Key|The shard key. You can use a column in the source table as the shard key.We recommend that you use the primary key or an indexed column.

|No|
        |Data destination|Data source|The type and name of the connection to which you want to synchronize data.In this example, a Hologres connection is used.

|Yes|
        |Table|The name of the table to which you want to synchronize data.Click **Generate target table with one click** to create a table in Hologres for receiving the synchronized data. You can modify the SQL statement for creating the table based on your business needs. Make sure that the field types in the Hologres table have a one-to-one mapping with the field types in the source table.

You can also create a table in Hologres in advance for receiving the synchronized data.

|Yes|
        |Write mode|        -   The method used to synchronize data to Hologres. Valid values: SDK\(Fast write\) and SQL\(INSERT INTO\).

**SDK\(Fast write\)**: Write data to Hologres by using the HoloHub API. For more information, see [Overview of the HoloHub API](/intl.en-US/Data Access/Real-time data writing/Realtime Compute/Blink exclusive/Overview of the HoloHub API.md).

**Note:** If you select **SDK\(Fast write\)**, you must use an exclusive resource group for Data Integration.

        -   **SQL\(INSERT INTO\)**: Write data to Hologres by using the `INSERT INTO` statement provided by PostgreSQL.
|Yes|
        |Write conflict strategy|The method used to cope with new data that conflicts with existing data in the destination Hologres table. Valid values: Replace and Ignore.

        -   **Replace**: Overwrite the existing data.
        -   **Ignore**: Retain the existing data and ignore the new data.
**Note:** This parameter is available only when the table has a primary key.

|Yes|

    2.  In the **Field Mapping** step, configure field mappings to import all or some fields.

    3.  In the **Channel control** step, set parameters as needed.

        ![Channel control](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7755901061/p140955.png)

        |Parameter|Description|Required|
        |---------|-----------|--------|
        |Maximum number of concurrent tasks expected|The maximum number of concurrent threads that the batch sync node uses to read data from the source data store and write data to the destination data store.|Yes|
        |Synchronization rate|Specifies whether to enable bandwidth throttling. Valid values: Unlimited flow and Current limit.

        -   **Current limit**: Enable bandwidth throttling and set a maximum transmission rate to avoid heavy read and write workload of the source and destination.
        -   **Unlimited flow**: The system provides the maximum transmission rate that is allowed by the hardware.
We recommend that you select **Current limit** and set the maximum transmission rate based on the configuration of the batch sync node.

|Yes|
        |The number of error records exceeds|The maximum number of dirty data records allowed.|No|

    4.  Configure an exclusive resource group for Data Integration.

        1.  On the configuration tab of the batch sync node, click **Data integration resource group configuration** in the right-side navigation pane.
        2.  On the **Data integration resource group configuration** tab, select **Exclusive data integration Resource Group** for **Programme** and select an existing exclusive resource group from the **Exclusive data integration Resource Group** drop-down list. You can also click **Create a new exclusive Resource Group** to create an exclusive resource group.

            **Note:**

            1.  If you select **SDK\(Fast write\)** for **Write mode**, you must configure an exclusive resource group for Data Integration.
            2.  When you use an exclusive resource group to synchronize data from an ApsaraDB RDS for MySQL instance, you must configure a virtual private cloud \(VPC\) for the exclusive resource group. For more information, see [Exclusive resource group mode]().
            3.  Make sure that the exclusive resource group resides in the same zones as the VPC of the ApsaraDB RDS for MySQL instance.
    5.  After you complete the preceding configuration, click the ![Save icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8421821061/p140970.png) icon at the top of the page to save the batch sync node.

    6.  Click the ![Run icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8421821061/p140974.png) icon at the top of the page to run the batch sync node.

6.  View the synchronized data in Hologres.

    For example, you can use the following SQL statement to query synchronized data:

    ```
    SELECT * FROM rds_test;
    ```


