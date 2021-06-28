# Quick BI

Alibaba Cloud Quick BI allows you to analyze a large amount of data online in real time. It supports drag-and-drop operations and rich visualization features to help you easily analyze data, explore business data, and produce reports. This topic describes how to use Alibaba Cloud Quick BI to connect to Hologres and analyze data in a visualized manner.

1.  **Log on to the Quick BI console.**

    Search for Quick BI on the Alibaba Cloud official website and log on to the [Quick BI console](https://www.alibabacloud.com/product/quickbi?spm=a2c5t.10695662.3156523820.dnavproductdatasearch2.6e495cbfdFUvyA). If you have not activated the Quick BI service, activate it. For more information, see [Purchases, upgrades, downgrades, renewals, and overdue payments]().

2.  **Add a data source.**

    In the Quick BI console, click **Workspace** in the top navigation bar. On the page that appears, click **Data Sources** in the left-side navigation pane. On the Data Sources page that appears, click **Create Data Source** in the upper-right corner. In the Add Data Source dialog box that appears, click **PostgreSQL** in the **Cloud Databases** section. The Add PostgreSQL Database dialog box appears.

3.  **Set parameters of the data source.**

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the data source. Enter a custom name.|
    |Database Address|The endpoint of the Hologres instance on the Internet.|
    |Port Number|The port number of the Hologres instance on the Internet.|
    |Schema|The name of the schema in the specified database. The default value is public.|
    |Username|The AccessKey ID of the account that you use to connect to Hologres.|
    |Password|The AccessKey secret of the account that you use to connect to Hologres.|
    |VPC Data Source|Specifies whether the data source is in a Virtual Private Cloud \(VPC\). Clear this check box.|
    |SSL|Specifies whether to connect to Hologres by using SSL. Clear this check box.|

    After setting all the parameters, click **Test Connection** to test the connectivity between the data source and Hologres. If the connectivity test is successful, click **OK**. The data source is added.

4.  **Analyze data in a visualized manner in Quick BI.**

    The added data source appears on the **Data Sources** page. Click the data source under My Data Sources. All tables, including foreign tables, in the data source appear on the right side. You can directly select a table in a data source to analyze. You can also execute an SQL statement to query the desired data to analyze.

    -   **Directly select a table in a data source to analyze.**

        To select an internal table of Hologres and analyze the table data, follow these steps:

        1.  On the Workspace tab of the Quick BI console, click **Data Sources** in the left-side navigation pane. On the Data Sources page that appears, find the desired internal table in the target data source and click the **Create Dataset** icon in the Actions column.
        2.  In the Create Dataset dialog box that appears, set the Name and Save To parameters and click OK. On the **Datasets** page that appears, you can view information about the internal table. You can click the **Edit** icon in the Actions column to view the data in the table. You can also click the **Create Dashboard** icon to generate a chart based on the table data.

            In this example, click Create Dashboard. On the page that appears, select Stacked Vertical Bar Chart from the Vertical Bar Chart drop-down list. Edit the table data and specify the chart style in the **Graphic Design** section.

            For more information about drag-and-drop operations on the dashboard, see [Dashboard overview]().

    -   **Execute an SQL statement to query the desired data to analyze.**

        You can also execute an SQL statement to query the desired data to analyze. To query data in a foreign table of Hologres and analyze the data, follow these steps:

        1.  On the Workspace tab of the Quick BI console, click **Data Sources** in the left-side navigation pane. On the Data Sources page that appears, select a data source and click **Ad Hoc Query** in the upper-right corner.
        2.  On the **Ad Hoc Query** page that appears, enter an SQL statement and click **Run** to query the desired data. Then, click **Create Dataset** in the upper-right corner to create a dataset. Then you can analyze the table data in a visualized manner in **Datasets**. For more information, see [Use ad hoc queries for data modeling]().

