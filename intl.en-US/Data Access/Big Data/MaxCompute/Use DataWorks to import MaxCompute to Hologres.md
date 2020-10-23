# Use DataWorks to import MaxCompute to Hologres

This topic describes how to use Data Integration and DataStudio of DataWorks to import data from MaxCompute tables to Hologres.

## Prerequisites

1.  DataWorks is activated. For more information, see [Overview]().
2.  A Hologres instance is purchased and bound to a DataWorks workspace. For more information, see [Quick start to HoloStudio](/intl.en-US/HoloStudio/Quick start to HoloStudio.md).
3.  MaxCompute is activated. For more information, see [Activate MaxCompute](/intl.en-US/Prepare/Activate MaxCompute.md).

**Note:** This topic describes how to import data from a MaxCompute non-partition table and a MaxCompute partition table to Hologres separately. Hologres seamlessly integrates with MaxCompute at the underlying layer. We recommend that you execute SQL statements to import data from MaxCompute tables to Hologres. For more information, see [t1644102.md\#](/intl.en-US/Data Access/Big Data/MaxCompute/Use SQL statements to import MaxCompute to Hologres.md).

## Import MaxCompute non-partition table to Hologres

To import data from a MaxCompute non-partition table to Hologres, follow these steps:

1.  **Prepare a MaxCompute non-partition table.**

    Create a MaxCompute non-partition table and import data to the table. For more information, see [Create and view a table](/intl.en-US/Quick Start/Create and view a table.md). You can also use an existing MaxCompute non-partition table. This example uses an existing MaxCompute non-partition table. The sample data definition language \(DDL\) statement is as follows:

    ```
    CREATE TABLE IF NOT EXISTS bank_data
    (
        age            BIGINT COMMENT 'age',
        job            STRING COMMENT 'job type',
        marital        STRING COMMENT 'marital status',
        education      STRING COMMENT 'education level',
        creditcard     STRING COMMENT 'credit card available or not',
        housing        STRING COMMENT 'mortgage',
        loan           STRING COMMENT 'loan',
        contact        STRING COMMENT 'contact',
        month          STRING COMMENT 'month',
        day_of_week    STRING COMMENT 'day of the week',
        duration       STRING COMMENT 'duration',
        campaign       BIGINT COMMENT 'number of contacts during the campaign',
        pdays          DOUBLE COMMENT 'interval from the last contact',
        previous       DOUBLE COMMENT 'number of contacts with the customer',
        poutcome       STRING COMMENT 'result of the previous marketing campaign',
        emp_var_rate   DOUBLE COMMENT 'employment change rate',
        cons_price_idx DOUBLE COMMENT 'consumer price index',
        cons_conf_idx  DOUBLE COMMENT 'consumer confidence index',
        euribor3m      DOUBLE COMMENT 'euro deposit rate',
        nr_employed    DOUBLE COMMENT 'number of employees',
        y              BIGINT COMMENT 'fixed time deposit available or not'
    ) ;
    ```

2.  **Configure a connection to Hologres.**

    Before using Data Integration of DataWorks to import data to Hologres, you must configure a connection to Hologres. Log on to the DataWorks console and go to the Data Integration page of the target workspace. On the homepage of Data Integration, click the **Workspace Manage** icon in the upper-right corner. On the page that appears, click **Data Source** in the left-side navigation pane. On the Data Source page, click **Add a Connection** in the upper-right corner. In the dialog box that appears, click **Hologres**. In the Add Hologres Connection dialog box, set parameters as required.

    ![c](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5098317951/p127045.png)

3.  **Use Data Integration to import data from MaxCompute non-partition table to Hologres.**
    1.  After configuring the connection, go back to the homepage of **Data Integration** and click **New Task**. Then, you are directed to DataStudio. In the Create Node dialog box that appears, select Batch Synchronization for Node Type, set Node Name and Location as required, and then click Commit.

        ![738](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5098317951/p96502.png)

    2.  In the **Connections** section on the configuration tab of the node, specify the MaxCompute non-partition table as the source. Specify a Hologres connection as the destination and click **Create Table** to create a table in Hologres for receiving data from the MaxCompute non-partition table. You can also set an existing table in Hologres as the destination if it supports the same data types as the MaxCompute non-partition table.

        |GUI element|Description|Required|Remarks|
        |-----------|-----------|--------|-------|
        |Create Table|The button for creating a table in Hologres for receiving data from the source MaxCompute table. This Hologres table contains the same fields as the source MaxCompute table.|No|You can also create a table in Hologres in advance for receiving data from the source MaxCompute table. Make sure that this Hologres table supports the same data types as the source MaxCompute table.|
        |Partitions|The partitions to be synchronized. If the source MaxCompute table is non-partition, this parameter is unavailable.|No|For more information about how to import data from a MaxCompute partition table to Hologres, see the "Import data from a MaxCompute partition table to Hologres" section of this topic.|

    3.  Set **Write Mode** and **Solution to Data Write Conflicts**.

        |Parameter|Description|Required|Remarks|
        |---------|-----------|--------|-------|
        |Partitions|The partitions to which data is synchronized.|Yes|You can set partitioning fields to variables or fixed values.|
        |Write Mode|The method used to import data to Hologres. Valid values: SDK \(Fast Write\) and SQL \(INSERT INTO\).         -   SDK \(Fast Write\): Write data to Hologres through the HoloHub API. This mode provides the optimal performance for you.
        -   SQL \(INSERT INTO\): Write data to Hologres through the INSERT INTO statement provided by PostgreSQL.
|Yes|To write data in the SDK \(Fast Write\) mode, use **the HoloHub API**. For more information, see [Overview of the HoloHub API](/intl.en-US/Data Access/Big Data/Realtime Compute/Overview of the HoloHub API.md).|
        |Solution to Data Write Conflicts|Specifies whether to overwrite the existing data in the destination Hologres table. Valid values: Replace and Ignore.         -   Replace: Overwrite the existing data.
        -   Ignore: Retain the existing data and ignore the new data.
|Yes|**This parameter is available only when the table has a primary key.**|

    4.  Configure field mappings to import all or some fields.

        |GUI element|Description|
        |-----------|-----------|
        |Map Fields with the Same Name|Click Map Fields with the Same Name to establish a mapping between fields with the same name. Note that the data types of the fields must match.|
        |Map Fields in the Same Line|Click Map Fields in the Same Line to establish a mapping between fields in the same row. Note that the data types of the fields must match.|
        |Delete All Mappings|Click Delete All Mappings to remove mappings that have been established.|
        |Auto Layout|Click Auto Layout. The fields are automatically sorted based on specified rules.|
        |Change Fields|Click the Change Fields icon. In the Change Fields dialog box that appears, you can manually edit fields in the source table. Each field occupies a row. The first and the last blank rows are included, whereas other blank rows are ignored.|
        |Add|        -   Click Add to add a field. You can enter constants. Each constant must be enclosed in single quotation marks \(' '\), such as 'abc' and '123'.
        -   You can use scheduling parameters, such as $\{bizdate\}.
        -   You can enter functions supported by relational databases, such as now\(\) and count\(1\).
        -   Fields that cannot be parsed are indicated by Unidentified. |

    5.  Configure channel control policies. **If you set the write mode to SDK \(Fast Write\), you must specify an exclusive resource group for data integration.**

        |Parameter|Description|Required|Remarks|
        |---------|-----------|--------|-------|
        |Expected Maximum Concurrency|The maximum number of concurrent threads to read and write data to data storage within a single sync node.|No|None.|
        |Bandwidth Throttling|Specifies whether to enable bandwidth throttling. You can enable bandwidth throttling and set a maximum transmission rate to avoid heavy read workload of the source.|No|We recommend that you enable bandwidth throttling and set the maximum transmission rate based on the configurations of the source database.|
        |Dirty Data Records Allowed|The maximum number of errors or dirty data records allowed.|No|None.|
        |Resource Group|The group of the servers on which the batch sync node is run. To write data in the SDK \(Fast Write\) mode, you must select an exclusive resource group for data integration.|Yes|For more information, see [Use exclusive resource groups for data integration]() and [Add a custom resource group]().|

    6.  After the preceding configuration is completed, click the **Save** icon and then the **Run** icon on the top of the tab. The system synchronizes the specified data from the source MaxCompute table to Hologres.
4.  **Query data in Hologres.**

    After the node is run, you can query data written to the table in Hologres. A sample query statement is as follows:

    ```
    SELECT * from bank_data;
    ```


## Import MaxCompute partition table to Hologres

Hologres seamlessly integrates with MaxCompute. Data in a MaxCompute partition table can be imported to a partition or non-partition table in Hologres. This section describes both of the two cases.

1.  **Prepare a MaxCompute partition table.**

    Create a MaxCompute partition table and import data to the table. You can also use an existing MaxCompute partition table. This example uses an existing MaxCompute partition table. The sample DDL statement is as follows:

    ```
    create table odps_table
    (
        shop_name     string,
        customer_id   string,
        total_price   INT 
    )
    partition by (sale_date string ,sale_time string);
    ```

    The following figure shows some data in the table.

2.  **Configure a connection to Hologres.**

    Before using Data Integration of DataWorks to import data to Hologres, you must configure a connection to Hologres. Log on to the DataWorks console and go to the Data Integration page of the target workspace. On the homepage of Data Integration, click the **Workspace Manage** icon in the upper-right corner. On the page that appears, click **Data Source** in the left-side navigation pane. On the Data Source page, click **Add a Connection** in the upper-right corner. In the dialog box that appears, click **Hologres**. In the Add Hologres Connection dialog box, set parameters as required. .

3.  **Use Data Integration to import MaxCompute partition table to Hologres.**
    -   **Case 1: Import MaxCompute partition table to a Hologres non-partition table.**
        1.  Create a batch sync node. Set the MaxCompute partition table as the source and configure the partition information. For more information, see [Scheduling parameters]().
        2.  Specify a Hologres connection as the destination and click **Create Table** to create a non-partition table in Hologres for receiving data from the MaxCompute partition table. To synchronize the partition information of the source MaxCompute table to the destination Hologres table, specify partitioning fields in the SQL statements for creating the Hologres table. The following figure shows the sample statements.
        3.  Set **Write Mode** and **Solution to Data Write Conflicts**. For more information, see [Hologres Writer]().

            |Parameter|Description|Required|Remarks|
            |---------|-----------|--------|-------|
            |Partitions|The partitions to which data is synchronized.|Yes|You can set partitioning fields to variables or fixed values.|
            |Write Mode|The method used to import data to Hologres. Valid values: SDK \(Fast Write\) and SQL \(INSERT INTO\).             -   SDK \(Fast Write\): Write data to Hologres through the HoloHub API. This mode provides the optimal performance for you.
            -   SQL \(INSERT INTO\): Write data to Hologres through the INSERT INTO statement provided by PostgreSQL.
|Yes|To write data in the SDK \(Fast Write\) mode, use the HoloHub API. For more information, see [Overview of the HoloHub API](/intl.en-US/Data Access/Big Data/Realtime Compute/Overview of the HoloHub API.md).|
            |Solution to Data Write Conflicts|Specifies whether to overwrite the existing data in the destination Hologres table. Valid values: Replace and Ignore.             -   Replace: Overwrite the existing data.
            -   Ignore: Retain the existing data and ignore the new data.
|Yes|**This parameter is available only when the table has a primary key.**|

        4.  Click **Properties** in the right-side pane and assign values to the partitioning fields based on your business requirements, as shown in the following figure.
        5.  In the Mappings section, manually add the partitioning fields of the MaxCompute table and map them to the corresponding fields in the Hologres table, as shown in the following figure. This is because the partitioning fields of the MaxCompute table are not displayed by default.
        6.  Configure channel control policies. **If you set the write mode to SDK \(Fast Write\), you must specify an exclusive resource group for data integration.**

            |Parameter|Description|Required|Remarks|
            |---------|-----------|--------|-------|
            |Expected Maximum Concurrency|The maximum number of concurrent threads to read and write data to data storage within a single sync node.|No|None.|
            |Bandwidth Throttling|Specifies whether to enable bandwidth throttling. You can enable bandwidth throttling and set a maximum transmission rate to avoid heavy read workload of the source.|No|We recommend that you enable bandwidth throttling and set the maximum transmission rate based on the configurations of the source database.|
            |Dirty Data Records Allowed|The maximum number of errors or dirty data records allowed.|No|None.|
            |Resource Group|The group of the servers on which the batch sync node is run. To write data in the SDK \(Fast Write\) mode, you must select an exclusive resource group for data integration.|Yes|For more information, see [Use exclusive resource groups for data integration]() and [Add a custom resource group]().|

        7.  Save and run the batch sync node. Then, the system synchronizes the data of the specified partitions to the Hologres non-partition table. You can also commit the batch sync node to the scheduling system to periodically import the specified data in the source MaxCompute table to the destination Hologres table. For more information, see [Dependencies]().
    -   **Case 2: Import MaxCompute partition table to a Hologres partition table.**

        If you set the write mode to SDK \(Fast Write\) mode, you can import data from a MaxCompute partition table to a parent partition table in Hologres. Then, the system routes the data in different partitions to the corresponding child partition tables. The procedure is as follows:

        1.  Specify a MaxCompute partition table as the source and configure the partition information for it. For more information, see [Scheduling parameters]().
        2.  Specify a Hologres connection as the destination and click **Create Table** to create a partition table in Hologres for receiving data from the MaxCompute partition table. The sample SQL statements for creating a Hologres partition table are as follows:

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

            **Note:** You can also import data from a MaxCompute partition to a non-partition table in Hologres. In addition, Hologres tables do not support multi-level partitions. Configure the partitions for the Hologres table as needed.

        3.  Configure the partition information and data writing policy for the destination Hologres table, as described in the following table.

            |Parameter|Description|Required|Remarks|
            |---------|-----------|--------|-------|
            |Partitions|The partitions to which data is synchronized.|Yes|You can set partitioning fields to variables or fixed values.|
            |Write Mode|The method used to import data to Hologres. Valid values: SDK \(Fast Write\) and SQL \(INSERT INTO\).             -   SDK \(Fast Write\): Write data to Hologres through the HoloHub API. This mode provides the optimal performance for you.
            -   SQL \(INSERT INTO\): Write data to Hologres through the INSERT INTO statement provided by PostgreSQL.
|Yes|To write data in the SDK \(Fast Write\) mode, use **the HoloHub API**. For more information, see [Overview of the HoloHub API](/intl.en-US/Data Access/Big Data/Realtime Compute/Overview of the HoloHub API.md).|
            |Solution to Data Write Conflicts|Specifies whether to overwrite the existing data in the destination Hologres table. Valid values: Replace and Ignore.             -   Replace: Overwrite the existing data.
            -   Ignore: Retain the existing data and ignore the new data.
|Yes|**This parameter is available only when the table has a primary key.**|

        4.  Click **Properties** in the right-side pane and assign values to the partitioning fields based on your business requirements, as shown in the following figure.
        5.  In the Mappings section, manually add the partitioning fields of the MaxCompute table and map them to the corresponding fields in the Hologres table, as shown in the following figure. This is because the partitioning fields of the MaxCompute table are not displayed by default.
        6.  Configure channel control policies. **If you set the write mode to SDK \(Fast Write\), you must specify an exclusive resource group for data integration.**
        7.  Save and run the batch sync node. Then, the system synchronizes the data of the specified partitions to the Hologres partition table. You can also commit the batch sync node to the scheduling system to periodically import the specified data in the source MaxCompute table to the destination Hologres table. For more information, see [Dependencies]().
4.  **Query data in Hologres.**
    -   Case 1 imports the specified data in a MaxCompute partition table to a Hologres non-partition table. You can query the Hologres table in Hologres directly. The sample query statement is as follows:

        ```
        SELECT * FROM holo;
        ```

    -   Case 2 imports the specified data in a MaxCompute partition table to a Hologres partition table. After the batch sync node is run, a corresponding child partition table is generated in Hologres. You can query either the parent or child partition table in Hologres. The sample query statement is as follows:

        ```
        SELECT * FROM holo_test_1585817112344
        ```


