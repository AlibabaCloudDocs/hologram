---
keyword: [Quick BI, visualized data analysis, Hologres]
---

# Quick BI

Alibaba Cloud Quick BI allows you to analyze a large amount of data online in real time and provides abundant visualization features. This way, you can analyze data, explore business data, and produce reports with ease by performing drag-and-drop operations or executing SQL statements. This topic shows you how to connect Quick BI to Hologres and analyze data in a visualized manner.

-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).
-   Quick BI is activated. For more information, see [Purchases, upgrades, downgrades, renewals, and overdue payments]().

Quick BI can be efficiently connected to Hologres. This way, you can directly query Hologres data in Quick BI for visualized data analysis. You can use one of the following two types of connections to connect Quick BI to Hologres:

-   Hologres connection: If you use Quick BI Pro or Quick BI Standard, you can configure a Hologres connection to connect Quick BI to Hologres for visualized data analysis. Compared with a PostgreSQL connection, this type of connection is more compatible with Hologres and easier to use. Therefore, we recommend that you use this type of connection.
-   PostgreSQL connection: If you use Quick BI Basic, you can configure a PostgreSQL connection to connect Quick BI to Hologres for visualized data analysis.

In this example, Quick BI Pro is connected to Hologres for visualized data analysis.

## Limits

-   You must connect Quick BI to Hologres by using a public endpoint or a classic network endpoint, but not a VPC endpoint.
-   Hologres does not allow you to configure a whitelist. Therefore, you can connect Quick BI to Hologres without the need to configure a whitelist.
-   You can use a Hologres connection or a PostgreSQL connection to connect Quick BI to Hologres. You can determine the connection type based on the edition of Quick BI. We recommend that you use a Hologres connection.

## Connect Quick BI to Hologres to perform visualized data analysis

1.  Log on to the [Quick BI console](http://das.base.shuju.aliyun.com/console.htm?spm=a2c0j.8190895.737583.btn2.34f87d6a2MBlQ6).

2.  Create a connection.

    1.  In the Quick BI console, click **Workspace** in the top navigation bar.

    2.  On the **Workspace** page, click **Data Sources** in the left-side navigation pane.

    3.  On the **Data Sources** page, click **Create Data Source** in the upper-right corner.

    4.  Select a type of connection that you want to configure to connect Quick BI to Hologres and set the required parameters. You can use a Hologres connection or a PostgreSQL connection to connect Quick BI to Hologres. You can determine the connection type based on the edition of Quick BI. We recommend that you use a Hologres connection.

        -   1.  Choose **Cloud Data Sources** \> **Hologres**.

    ![Select Hologres](../images/p190025.png)

2.  In the **Add Hologres Database** dialog box, set the parameters as required.

    ![Create a Hologres connection](../images/p189738.png)

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |Name|The custom name of the connection. |
    |Database Address|The endpoint of the Hologres instance.

You can view the endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres](https://hologram.console.aliyun.com/#/instance) console.

**Note:** You must connect Quick BI to Hologres by using a public endpoint or a classic network endpoint, but not a VPC endpoint. |
    |Port Number|The port number of the Hologres instance.

You can view the endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres](https://hologram.console.aliyun.com/#/instance) console.

**Note:** You must connect Quick BI to Hologres by using a public endpoint or a classic network endpoint, but not a VPC endpoint. |
    |Database|The name of the database to be connected in the Hologres instance.

You can view the name of the database on the **Databases** tab of the instance details page in the [Hologres](https://hologram.console.aliyun.com/#/instance) console. |
    |Schema|The name of the schema in the specified database. The default value is **public**. You can also use a newly created schema. |
    |Username|The AccessKey ID of the account that you use to connect to Hologres.

You can view the AccessKey ID on the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page. |
    |Password|The AccessKey secret of the account that you use to connect to Hologres.

You can view the AccessKey secret on the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page. |

        -   1.  Choose **Cloud Data Sources** \> **PostgreSQL**.

    ![Select PostgreSQL](../images/p190026.png)

2.  In the **Add PostgreSQL Database** dialog box, set the parameters as required.

    ![Create a PostgreSQL connection](../images/p189743.png)

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |Name|The custom name of the connection. |
    |Database Address|The endpoint of the Hologres instance.

You can view the endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres](https://hologram.console.aliyun.com/#/instance) console.

**Note:** You must connect Quick BI to Hologres by using a public endpoint or a classic network endpoint, but not a VPC endpoint. |
    |Port Number|The port number of the Hologres instance.

You can view the endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres](https://hologram.console.aliyun.com/#/instance) console.

**Note:** You must connect Quick BI to Hologres by using a public endpoint or a classic network endpoint, but not a VPC endpoint. |
    |Database|The name of the database to be connected in the Hologres instance.

You can view the name of the database on the **Databases** tab of the instance details page in the [Hologres](https://hologram.console.aliyun.com/#/instance) console. |
    |Schema|The name of the schema in the specified database. The default value is **public**. You can also use a newly created schema. |
    |Username|The AccessKey ID of the account that you use to connect to Hologres.

You can view the AccessKey ID on the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page. |
    |Password|The AccessKey secret of the account that you use to connect to Hologres.

You can view the AccessKey secret on the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page. |
    |VPC Data Source|Specifies whether the data source is in a VPC. Do not select this check box.You must connect Quick BI to Hologres by using a public endpoint or a classic network endpoint, but not a VPC endpoint. |
    |SSL|Specifies whether to connect to Hologres by using SSL. Do not select this check box. SSL is not supported when you connect Quick BI to Hologres.|

        After you set the parameters, you can click **Test Connection** to test the connection between Hologres and Quick BI.

        -   If the **The add operation is completed** message appears, the connection between Hologres and Quick BI is established.
        -   If the **Quick BI cannot connect to the specified data source. Verify that all settings are correct** message appears, the connection between Hologres and Quick BI fails to be established. Handle the exception based on the error message.
    5.  Click **OK**.

3.  Analyze data in a visualized manner.

    After the data source is connected, click the created connection in the **My Data Sources** section of the **Data Sources** page. Then, all tables in the data source are displayed. You can analyze data in a visualized manner by using one of the following methods:

    -   Perform drag-and-drop operations on a visualized user interface \(UI\).
    -   Execute an SQL statement for ad hoc queries.
    To use the two methods to analyze data, perform the following steps:

    -   Perform drag-and-drop operations.
        1.  Find a table for which you want to create a dataset and click the ![Create Dataset](../images/p167521.png) icon in the **Actions** column to create a dataset.
        2.  In the **Create Dataset** dialog box, set the **Name** and **Save To** parameters.

            ![Create Dataset](../images/p188765.png)

        3.  Click **OK**.
        4.  On the **Datasets** page, find the dataset that you want to manage and click an icon in the **Actions** column to perform the corresponding operation:

            ![Actions column of the dataset](../images/p188773.png)

            -   Click the ![Edit](../images/p167525.png) icon and view the data of the current dataset.
            -   Click the ![Create a dashboard](../images/p167526.png) icon and create a dashboard. Import data of the table to generate a chart for visualized display.
            -   Click the ![Create a workbook](../images/p167527.png) icon and create a workbook. Import data of the table to generate a workbook for visualized display.
            -   Click the ![More](../images/p167528.png) icon and perform other operations, such as data de-identification, cache configuration, and cache clearing.
            For more information, see [Dashboard overview]().

            The following figure shows a newly created dashboard. On the dashboard editing page, you can create a stacked vertical bar chart and configure a chart style to display data.

            ![Show a newly created dashboard](../images/p188790.png)

    -   Execute an SQL statement for ad hoc queries.

        1.  On the **Data Sources** page, click **Ad Hoc Query** in the upper-right corner to go to the **Ad Hoc Query** page.
        2.  Enter an SQL statement to query data based on your business requirements and click **Run**.

            ![SQL statement for ad hoc queries](../images/p188813.png)

        3.  After you obtain the data that you want, click **Create Dataset**.
        4.  In the **Save Custom SQL** dialog box, set the **Name**, **Save To**, and **SQL** parameters.

            ![Configure and save a custom SQL statement](../images/p188814.png)

        5.  Click **OK**.
        After a dataset is created, you can analyze the data in a visualized manner on the **Datasets** page. For more information, see [Use ad hoc queries for data modeling]().


