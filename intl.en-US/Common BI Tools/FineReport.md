---
keyword: [FineReport, Hologres, BI tool, visualized data analysis]
---

# FineReport

This topic describes how to connect FineReport to Hologres and how to use FineReport to create reports for data analysis in a visualized manner.

-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).
-   A solution is selected based on your project requirements and a required client is downloaded from the [FineReport official website](http://www.fanruan.com/) and is installed. In this example, [FineReport](http://www.fanruan.com/finereport) is downloaded and installed.

FineReport is an enterprise-level web report software. It is professional, simple, and flexible. It allows you to perform simple drag-and-drop operations to create various reports that provide visualized data to help decision making.

1.  Start FineReport. In the top navigation bar, choose **Server** \> **Define Data Connection**.

2.  In the **Define Data Connection** dialog box, configure the parameters listed in the following table.

    |Parameter|Description|Example value|
    |---------|-----------|-------------|
    |DB|You must set this parameter to **Postgre** when you connect FineReport to Hologres.

|**Postgre**|
    |Driver|The driver used to connect to Hologres. Select the built-in driver `org.postgresql.Driver`.After you add a database to FineReport, a driver is automatically generated.

|None|
    |URL|The public URL of the Hologres instance, which is in the `jdbc:postgresql://localhost:port/dbname` format.|`jdbc:postgresql://demo-cn-hangzhou.hologres.aliyuncs.com:80/postgres`|
    |User Name|The AccessKey ID of your Alibaba Cloud account.You can obtain the AccessKey ID on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).

|None|
    |Password|The AccessKey secret of your Alibaba Cloud account.You can obtain the AccessKey secret on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).

|None|

3.  Click **Test Connection**. If the **Connection Successfully** message appears, FineReport is connected to Hologres.

    **Note:** If the connectivity test fails, check whether the PostgreSQL driver is installed. For more information, see [PostgreSQL data connection](https://help.finereport.com/doc-view-2563.html).

4.  Click **OK**.

5.  Create a report for data analysis in a visualized manner.

    After FineReport is connected to Hologres, you can use it to create reports for data analysis in a visualized manner. For more information, see [FineReport documentation](https://www.fanruan.com/).

    **Note:** FineReport allows you to analyze data in external tables of Hologres in a visualized manner. If you add a template dataset, external tables are not displayed. You must execute SQL statements to query desired data.

    Perform the following steps to create a column chart report based on the data in an external table of Hologres:

    1.  Start FineReport. In the top navigation bar, choose **File** \> **New Dashboard**. On the page that appears, click the column chart icon in the Chart section, as shown in the following figure.

    2.  In the left-side navigation pane, click the Add icon above the **Template Dataset** tab and click **DB Query**.

    3.  In the **DB Query** dialog box, specify an SQL statement used to query data in the editor on the right side and click **OK**. In this example, specify an SQL statement to query 200 random data records in a specific external table, as shown in the following figure.

        **Note:** External tables of Hologres are not displayed in the left-side navigation pane of the DB Query dialog box. You must specify an SQL statement in the editor on the right side to query desired data in an external table.

    4.  Return to the chart page and configure chart data based on your business requirements.

        Click the **Data** tab and configure the Category and Value parameters to specify the X axis and Y axis. In this example, set Category to job and Value to age. Configure the parameters on the **Style** and **Special Effect** tabs as required.

    5.  Click the ![Save](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2181013061/p168552.png) icon in the upper-left corner to save the settings.

    6.  Click the ![Preview](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2181013061/p168553.png) icon to view the report.

        The following figure shows the report created based on the external table of Hologres.For more information about the operations in FineReport, visit the [FineReport official website](https://www.fanruan.com/).


