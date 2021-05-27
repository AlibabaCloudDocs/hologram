---
keyword: [Data Integration, import data from MaxCompute, Hologres, batch synchronization]
---

# Import data from MaxCompute to Hologres by using the Data Integration service of DataWorks

This topic shows you how to import data from partitioned or non-partitioned MaxCompute tables to Hologres by using the batch synchronization feature of the Data Integration service of DataWorks.

-   DataWorks is activated. For more information, see [Overview]().
-   MaxCompute is activated. For more information, see [Activate MaxCompute](/intl.en-US/Prepare/Activate MaxCompute.md).

Hologres is seamlessly integrated with MaxCompute at the underlying layer. If you want to import a large amount of data from MaxCompute to Hologres, we recommend that you use SQL statements. For more information, see [Import data from MaxCompute to Hologres by executing SQL statements](/intl.en-US/Data Access/Batch synchronization/MaxCompute/Import data from MaxCompute to Hologres by executing SQL statements.md).

## Import data from a non-partitioned MaxCompute table to Hologres

1.  Prepare a non-partitioned table in MaxCompute.

    Create a non-partitioned table in MaxCompute and import data to the table. For more information, see [Create and view a table](/intl.en-US/Quick Start/Create and view a table.md). You can also use a non-partitioned MaxCompute table that already exists. For example, you can execute the following statement to create a table:

    ```
    CREATE TABLE IF NOT EXISTS bank_data
    (
        age            BIGINT COMMENT 'Age',
        job            STRING COMMENT 'Job type',
        marital        STRING COMMENT 'Marital status',
        education      STRING COMMENT 'Education level',
        creditcard     STRING COMMENT 'Credit card available or not',
        housing        STRING COMMENT 'Mortgage',
        loan           STRING COMMENT 'Loan',
        contact        STRING COMMENT 'Contact',
        month          STRING COMMENT 'Month',
        day_of_week    STRING COMMENT 'Day of the week',
        duration       STRING COMMENT 'Duration',
        campaign       BIGINT COMMENT 'Number of contacts during the campaign',
        pdays          DOUBLE COMMENT 'Time elapsed since the last contact',
        previous       DOUBLE COMMENT 'Number of contacts with the customer',
        poutcome       STRING COMMENT 'Result of the previous marketing campaign',
        emp_var_rate   DOUBLE COMMENT 'Employment change rate',
        cons_price_idx DOUBLE COMMENT 'Consumer price index',
        cons_conf_idx  DOUBLE COMMENT 'Consumer confidence index',
        euribor3m      DOUBLE COMMENT 'Euro deposit rate',
        nr_employed    DOUBLE COMMENT 'Number of employees',
        y              BIGINT COMMENT 'Time deposit available or not'
    ) ;
    ```

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

3.  Configure a MaxCompute connection.

    1.  On the **Data Source** page, click **New data source** in the upper-right corner.

    2.  In the Add data source dialog box, click **MaxCompute \(ODPS\)** in the **Big Data Storage** section.

    3.  In the **Add MaxCompute \(ODPS\) data source** dialog box, set the parameters as required.

        ![mysql](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6755901061/p139801.png)

        |Parameter|Description|
        |---------|-----------|
        |Data Source Name|The custom name of the MaxCompute connection.|
        |Data source description|The description of the connection.|
        |Environment|The environment where the connection can be used. Valid values:        -   **Development**
        -   **Production**
**Note:** This parameter is displayed only when the DataWorks workspace is in standard mode. |
        |ODPS Endpoint|The endpoint and port number that are used to connect to MaxCompute.|
        |Tunnel Endpoint|The endpoint and port number that are used to connect to MaxCompute Tunnel.|
        |ODPS project name|The name of the MaxCompute project.|
        |AccessKey ID|The AccessKey ID of your Alibaba Cloud account. You can obtain the AccessKey ID from the [AccessKey Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.|
        |AccessKey Secret|The AccessKey secret of your Alibaba Cloud account. You can obtain the AccessKey secret from the [AccessKey Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.|

4.  Create a batch sync node in the Data Integration service of DataWorks.

    1.  On the **DataOS Panel** page, click the ![More icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8421821061/p139848.png) icon on the left side of the top navigation bar.

    2.  On the menu that appears, choose All Products \> **Data Integration**.

    3.  On the homepage of Data Integration, click **New Batch Sync Node** in the **Develop Sync Node** section.

    4.  In the **Create Node** dialog box, set the **Node Type**, **Node Name**, and **Location** parameters.

        ![Parameter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7755901061/p139854.png)

        If you do not have an existing workflow, create one before you create a node. For more information, see [Create a workflow]().

5.  Configure the batch sync node.

    1.  On the configuration tab of the batch sync node, set the parameters in the **Connections** step.

        ![Connections](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7755901061/p140870.png)

        |Section|Parameter|Description|Required|
        |-------|---------|-----------|--------|
        |Source|Connection|The type and name of the source data store from which you want to synchronize data.In this example, set the type to **ODPS**.

|Yes|
        |Table|The name of the table from which you want to synchronize data.|Yes|
        |Partition Key Column|The partition from which you want to synchronize data. In this example, the value is **None** because the source table is a non-partitioned table.|No|
        |Preview|The button that allows you to preview the data to be synchronized.|No|
        |Target|Connection|The type and name of the destination data store to which you want to synchronize data.In this example, set the type to **Hologres**.

|Yes|
        |Table|The name of the table to which you want to synchronize data.You can click **Create Table** and create a table in Hologres for receiving the data to be synchronized. You can modify the SQL statements for creating the table based on your business needs. Make sure that the field types in the Hologres table have a one-to-one mapping with the field types in the source table.

You can also create a table in Hologres in advance for receiving the data to be synchronized.

|Yes|
        |Write mode|The method that you want to use to synchronize data to Hologres. Valid values: SDK\(Fast write\) and SQL\(INSERT INTO\).         -   **SDK\(Fast write\)**: Write data to Hologres by using the HoloHub API. For more information, see [Overview of the HoloHub API](/intl.en-US/Data Access/Real-time data writing/Realtime Compute/Blink exclusive/Overview of the HoloHub API.md).

**Note:** If you select **SDK\(Fast write\)**, you must use an exclusive resource group for Data Integration.

        -   **SQL\(INSERT INTO\)**: Write data to Hologres by using `INSERT INTO` statements that are provided by PostgreSQL.
|Yes|
        |Write conflict strategy|The method that you want to use when data to be written conflicts with existing data in the destination Hologres table. Valid values: Replace and Ignore.        -   **Replace**: Overwrite the existing data.
        -   **Ignore**: Retain the existing data and ignore the data to be written.
**Note:** This parameter is displayed only when the destination table has a primary key.

|Yes|

    2.  In the **Mappings** step, configure field mappings to import all or some fields.

        The following table describes the supported operations related to mappings.

        |Operation|Description|
        |---------|-----------|
        |Map Fields with the Same Name|Establish a mapping between two fields with the same name. The data types of the fields must match.|
        |Map Fields in the Same Line|Establish a mapping between two fields in the same row. The data types of the fields must match.|
        |Delete All Mappings|Remove mappings that have been established.|
        |Auto Layout|Sort the fields based on specified rules.|

    3.  In the **Channel** step, set the parameters as required.

        ![Channel](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7755901061/p140955.png)

        |Parameter|Description|Required|
        |---------|-----------|--------|
        |Expected Maximum Concurrency|The maximum number of concurrent threads that the batch sync node uses to read data from the source and write data to the destination.|No|
        |Bandwidth Throttling|Specifies whether to enable bandwidth throttling. Valid values: Enable and Disable.        -   **Enable**: Enable bandwidth throttling and set a maximum transmission rate to avoid heavy read and write workload of the source and destination.
        -   **Disable**: The system provides the maximum transmission rate that is allowed by the hardware.
We recommend that you select **Enable** and set a maximum transmission rate based on the configuration of the batch sync node.

|Yes|
        |Dirty Data Records Allowed|The maximum number of dirty data records allowed.|No|

    4.  Configure an exclusive resource group for Data Integration.

        1.  On the configuration tab of the batch sync node, click the **Resource Group configuration** tab in the right-side navigation pane.
        2.  In the **Resource Group configuration** panel, set the **Programme** parameter to **Exclusive Resource Groups** and select an existing exclusive resource group from the **Exclusive Resource Groups** drop-down list. You can also click **Create an exclusive data integration Resource Group** and create an exclusive resource group.

            **Note:**

            -   The exclusive resource group must be an exclusive resource group for Data Integration.
            -   When you use an exclusive resource group to synchronize data from a MaxCompute project, you must configure a virtual private cloud \(VPC\) for the exclusive resource group. For more information, see [Exclusive resource group mode]().
            -   Make sure that the exclusive resource group resides in the same zone as the VPC of the MaxCompute project.
    5.  Click the ![Save icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8421821061/p140970.png) icon in the top menu bar of the configuration tab to save the batch sync node.

    6.  Click the ![Run icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8421821061/p140974.png) icon in the top menu bar of the configuration tab to synchronize data.

6.  Query the synchronized data in Hologres.

    For example, you can execute the following statement to query the synchronized data:

    ```
    SELECT * from bank_data;
    ```


## Import data from a partitioned MaxCompute table to Hologres

Hologres allows you to import data from partitioned MaxCompute tables. You can import data from a partitioned MaxCompute table to a Hologres table regardless of whether the Hologres table is a partitioned table or not.

1.  Prepare a partitioned table in MaxCompute.

    Create a partitioned table in MaxCompute and import data to the table. For more information, see [Create and view a table](/intl.en-US/Quick Start/Create and view a table.md). You can also use a partitioned MaxCompute table that already exists. For example, you can execute the following statement to create a partitioned table:

    ```
    CREATE TABLE odps_table
    (
        shop_name     string,
        customer_id   string,
        total_price   INT 
    )
    PARTITIONED BY (sale_date string ,sale_time string);
    ```

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

3.  Configure a MaxCompute connection.

    1.  On the **Data Source** page, click **New data source** in the upper-right corner.

    2.  In the Add data source dialog box, click **MaxCompute \(ODPS\)** in the **Big Data Storage** section.

    3.  In the **Add MaxCompute \(ODPS\) data source** dialog box, set the parameters as required.

        ![mysql](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6755901061/p139801.png)

        |Parameter|Description|
        |---------|-----------|
        |Data Source Name|The custom name of the MaxCompute connection.|
        |Data source description|The description of the connection.|
        |Environment|The environment where the connection can be used. Valid values:        -   **Development**
        -   **Production**
**Note:** This parameter is displayed only when the DataWorks workspace is in standard mode. |
        |ODPS Endpoint|The endpoint and port number that are used to connect to MaxCompute.|
        |Tunnel Endpoint|The endpoint and port number that are used to connect to MaxCompute Tunnel.|
        |ODPS project name|The name of the MaxCompute project.|
        |AccessKey ID|The AccessKey ID of your Alibaba Cloud account. You can obtain the AccessKey ID from the [AccessKey Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.|
        |AccessKey Secret|The AccessKey secret of your Alibaba Cloud account. You can obtain the AccessKey secret from the [AccessKey Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.|

4.  Create a batch sync node in the Data Integration service of DataWorks.

    1.  On the **DataOS Panel** page, click the ![More icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8421821061/p139848.png) icon on the left side of the top navigation bar.

    2.  On the menu that appears, choose All Products \> **Data Integration**.

    3.  On the homepage of Data Integration, click **New Batch Sync Node** in the **Develop Sync Node** section.

    4.  In the **Create Node** dialog box, set the **Node Type**, **Node Name**, and **Location** parameters.

        ![Parameter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7755901061/p139854.png)

        If you do not have an existing workflow, create one before you create a node. For more information, see [Create a workflow]().

5.  Configure the batch sync node and query the data.

    Two procedures are provided in this section. The first procedure describes how to import data from a partitioned MaxCompute table to a partitioned Hologres table. The second procedure describes how to import data from a partitioned MaxCompute table to a non-partitioned Hologres table.

    -   Import data from a partitioned MaxCompute data to a partitioned Hologres table.
        1.  On the configuration tab of the batch sync node, set the parameters in the **Connections** step.

            |Section|Parameter|Description|
            |-------|---------|-----------|
            |Source|Connection|The type and name of the source data store from which you want to synchronize data.In this example, set the type to **ODPS**. |
            |Table|The name of the table from which you want to synchronize data.|
            |Partition Key Column|The partition from which you want to synchronize data. For more information, see [Configure scheduling parameters]().|
            |Preview|The button that allows you to preview the data to be synchronized.|
            |Target|Connection|The type and name of the destination data store to which you want to synchronize data.In this example, set the type to Hologres. |
            |Table|The name of the table to which you want to synchronize data.You can click **Create Table** and create a partitioned table in Hologres for receiving the data to be synchronized. You can modify the SQL statement for creating the partitioned table based on your business needs.

You can also create a partitioned table in Hologres in advance for receiving the data to be synchronized.

For example, you can execute the following statements to create a partitioned table:

            ```
begin;
create table holo_test (
 "shop_name" text,
 "customer_id" text,
 "total_price" int8,
 "sale_date" text,
 "sale_time" text
)
partition by  list (sale_date);
commit;
            ```

**Note:** A partitioned MaxCompute table and a partitioned Hologres table do not have a strong mapping relationship. Hologres tables do not support multi-level partitioning. You can configure partitions for the partitioned Hologres table based on your business needs. You can also create a parent partitioned table in Hologres in advance. |
            |Partition Key Column|The partition to which data is synchronized. You can set this parameter to a variable or a fixed value.|
            |Write mode|The method that you want to use to synchronize data to Hologres. Valid values: SDK\(Fast write\) and SQL\(INSERT INTO\).            -   **SDK\(Fast write\)**: Write data to Hologres by using the HoloHub API. For more information, see [Overview of the HoloHub API](/intl.en-US/Data Access/Real-time data writing/Realtime Compute/Blink exclusive/Overview of the HoloHub API.md).

**Note:** If you select **SDK\(Fast write\)**, you must use an exclusive resource group for Data Integration.

            -   **SQL\(INSERT INTO\)**: Write data to Hologres by using `INSERT INTO` statements that are provided by PostgreSQL. |
            |Write conflict strategy|The method that you want to use when data to be written conflicts with existing data in the destination Hologres table. Valid values: Replace and Ignore.            -   **Replace**: Overwrite the existing data.
            -   **Ignore**: Retain the existing data and ignore the data to be written.
**Note:** This parameter is displayed only when the destination table has a primary key. |

        2.  On the configuration tab of the batch sync node, click the **Properties** tab in the right-side navigation pane. In the Properties panel, set the **Arguments** parameter based on your business needs.
        3.  By default, the **Mappings** step does not display the partition fields of the source MaxCompute table. You must manually add the partition fields and map them to the fields of the destination Hologres table. The following table describes the supported operations related to mappings.

            |Operation|Description|
            |---------|-----------|
            |Map Fields with the Same Name|Establish a mapping between two fields with the same name. The data types of the fields must match.|
            |Map Fields in the Same Line|Establish a mapping between two fields in the same row. The data types of the fields must match.|
            |Delete All Mappings|Remove mappings that have been established.|
            |Auto Layout|Sort the fields based on specified rules.|

        4.  In the **Channel** step, set the parameters as required.

            ![Channel](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7755901061/p140955.png)

            |Parameter|Description|Required|
            |---------|-----------|--------|
            |Expected Maximum Concurrency|The maximum number of concurrent threads that the batch sync node uses to read data from the source and write data to the destination.|No|
            |Bandwidth Throttling|Specifies whether to enable bandwidth throttling. Valid values: Enable and Disable.            -   **Enable**: Enable bandwidth throttling and set a maximum transmission rate to avoid heavy read and write workload of the source and destination.
            -   **Disable**: The system provides the maximum transmission rate that is allowed by the hardware.
We recommend that you select **Enable** and set the maximum transmission rate based on the configuration of the batch sync node.

|No|
            |Dirty Data Records Allowed|The maximum number of dirty data records allowed.|No|

        5.  Configure an exclusive resource group for Data Integration.
            1.  On the configuration tab of the batch sync node, click the **Resource Group configuration** tab in the right-side navigation pane.
            2.  In the **Resource Group configuration** panel, set the **Programme** parameter to **Exclusive Resource Groups** and select an existing exclusive resource group from the **Exclusive Resource Groups** drop-down list. You can also click **Create an exclusive data integration Resource Group** and create an exclusive resource group.

                **Note:**

                -   The exclusive resource group must be an exclusive resource group for Data Integration.
                -   When you use an exclusive resource group to synchronize data from a MaxCompute project, you must configure a VPC for the exclusive resource group. For more information, see [Exclusive resource group mode]().
                -   Make sure that the exclusive resource group resides in the same zone as the VPC of the MaxCompute project.
        6.  Click the ![Save icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8421821061/p140970.png) icon in the top menu bar of the configuration tab to save the batch sync node.
        7.  Click the ![Run icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8421821061/p140974.png) icon in the top menu bar of the configuration tab to run the node to synchronize data.

            You can also commit the batch sync node to the scheduling system to periodically synchronize the specified data from the source MaxCompute table to the destination Hologres table. For more information, see [Instructions to configure scheduling dependencies]().

        8.  Query the synchronized data in Hologres.

            After the batch sync node is run, child partitioned tables are generated. You can query data in the parent partitioned table or a child partitioned table. For example, you can execute the following statement to query data in a child partitioned table.

            ```
            SELECT * FROM holo_test_1585817112344
            ```

    -   Import data from a partitioned MaxCompute table to a non-partitioned Hologres table.
        1.  On the configuration tab of the batch sync node, set the parameters in the **Connections** step.

            |Section|Parameter|Description|
            |-------|---------|-----------|
            |Source|Connection|The type and name of the source data store from which you want to synchronize data.In this example, set the type to **ODPS**. |
            |Table|The name of the table from which you want to synchronize data.|
            |Partition Key Column|The partition from which you want to synchronize data. For more information, see [Configure scheduling parameters]().|
            |Preview|The button that allows you to preview the data to be synchronized.|
            |Target|Connection|The type and name of the destination data store to which you want to synchronize data.In this example, set the type to Hologres. |
            |Table|The name of the table to which you want to synchronize data.You can click **Create Table** and create a non-partitioned table in Hologres for receiving the data to be synchronized. You must define the fields corresponding to the partition fields of the source MaxCompute table in the SQL statement for creating the destination Hologres table.

You can also create a non-partitioned table in Hologres in advance for receiving the data to be synchronized. |
            |Partition Key Column|The partition to which data is synchronized. You can set this parameter to a variable or a fixed value.|
            |Write mode|The method that you want to use to synchronize data to Hologres. Valid values: SDK\(Fast write\) and SQL\(INSERT INTO\).            -   **SDK\(Fast write\)**: Write data to Hologres by using the HoloHub API. For more information, see [Overview of the HoloHub API](/intl.en-US/Data Access/Real-time data writing/Realtime Compute/Blink exclusive/Overview of the HoloHub API.md).

**Note:** If you select **SDK\(Fast write\)**, you must use an exclusive resource group for Data Integration.

            -   **SQL\(INSERT INTO\)**: Write data to Hologres by using `INSERT INTO` statements that are provided by PostgreSQL. |
            |Write conflict strategy|The method that you want to use when data to be written conflicts with existing data in the destination Hologres table. Valid values: Replace and Ignore.            -   **Replace**: Overwrite the existing data.
            -   **Ignore**: Retain the existing data and ignore the data to be written.
**Note:** This parameter is displayed only when the destination table has a primary key. |

            **Note:** We recommend that you select **SDK\(Fast write\)** for the Write mode parameter. To write data in **SDK\(Fast Write\)** mode, make sure that you have activated the HoloHub API. You can [submit a ticket](https://workorder-intl.console.aliyun.com/) to activate it.

        2.  On the configuration tab of the batch sync node, click the **Properties** tab in the right-side navigation pane. In the Properties panel, set the **Arguments** parameter based on your business needs.
        3.  By default, the **Mappings** step does not display the partition fields of the source MaxCompute table. You must manually add the partition fields and map them to the fields of the destination Hologres table. The following table describes the supported operations related to mappings.

            |Operation|Description|
            |---------|-----------|
            |Map Fields with the Same Name|Establish a mapping between two fields with the same name. The data types of the fields must match.|
            |Map Fields in the Same Line|Establish a mapping between two fields in the same row. The data types of the fields must match.|
            |Delete All Mappings|Remove mappings that have been established.|
            |Auto Layout|Sort the fields based on specified rules.|

        4.  In the **Channel** step, set the parameters as required.

            ![Channel](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7755901061/p140955.png)

            |Parameter|Description|Required|
            |---------|-----------|--------|
            |Expected Maximum Concurrency|The maximum number of concurrent threads that the batch sync node uses to read data from the source and write data to the destination.|No|
            |Bandwidth Throttling|Specifies whether to enable bandwidth throttling. Valid values: Enable and Disable.            -   **Enable**: Enable bandwidth throttling and set a maximum transmission rate to avoid heavy read and write workload of the source and destination.
            -   **Disable**: The system provides the maximum transmission rate that is allowed by the hardware.
We recommend that you select **Enable** and set the maximum transmission rate based on the configuration of the batch sync node.

|No|
            |Dirty Data Records Allowed|The maximum number of dirty data records allowed.|No|

        5.  Configure an exclusive resource group for Data Integration.
            1.  On the configuration tab of the batch sync node, click the **Resource Group configuration** tab in the right-side navigation pane.
            2.  In the **Resource Group configuration** panel, set the **Programme** parameter to **Exclusive Resource Groups** and select an existing exclusive resource group from the **Exclusive Resource Groups** drop-down list. You can also click **Create an exclusive data integration Resource Group** and create an exclusive resource group.

                **Note:**

                1.  The exclusive resource group must be an exclusive resource group for Data Integration.
                2.  When you use an exclusive resource group to synchronize data from a MaxCompute project, you must configure a VPC for the exclusive resource group. For more information, see [Exclusive resource group mode]().
                3.  Make sure that the exclusive resource group resides in the same zone as the VPC of the MaxCompute project.
            3.  Click the ![Save icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8421821061/p140970.png) icon in the top menu bar of the configuration tab to save the batch sync node.
            4.  Click the ![Run icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8421821061/p140974.png) icon in the top menu bar of the configuration tab to run the node to synchronize data.

                You can also commit the batch sync node to the scheduling system to periodically synchronize the specified data from the source MaxCompute table to the destination Hologres table. For more information, see [Instructions to configure scheduling dependencies]().

            5.  Query the synchronized data in Hologres.

                After the batch sync node is run, you can query the synchronized data in the destination table in Hologres. For example, you can execute the following statement to query the synchronized data:

                ```
                SELECT * FROM holo;
                ```


