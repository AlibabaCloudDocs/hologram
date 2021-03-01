---
keyword: [BI tool, visualized data analysis, Tableau, Tableau Server]
---

# Tableau

This topic describes how to connect Tableau to Hologres. After Tableau is connected to Hologres, you can analyze data in Hologres in a visualized manner.

Tableau is a secure and flexible end-to-end data analysis platform. It provides a complete set of features from connection setup to collaboration. Hologres is compatible with PostgreSQL. You can connect Tableau to Hologres for data analysis in a visualized manner.

## Connect Tableau Desktop to Hologres

1.  Download and install Tableau.

    Visit the [Tableau official website](https://www.tableau.com/zh-cn/products) and download a Tableau client based on your business requirements. Install the client as prompted. In this example, download and install [Tableau Desktop](https://www.tableau.com/zh-cn/products/desktop).

2.  Establish a connection to Hologres.

    1.  Start Tableau Desktop.

    2.  In the left-side navigation pane, choose **Connect** \> **To a Server** \> **PostgreSQL**. In the PostgreSQL dialog box, set the parameters for a connection to Hologres.

        The following table describes the parameters.

        |Parameter|Description|
        |---------|-----------|
        |Server|The public endpoint of the Hologres instance.You can view the public endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
        |Port|The port number of the Hologres instance.You can view the port number of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
        |Database|The name of the Hologres database.You can view the name of the database on the **Databases** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
        |Authentication|The authentication method. Select **User Name and Password**.|
        |User Name|The AccessKey ID of your Alibaba Cloud account.You can view the AccessKey ID on the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page. |
        |Password|The AccessKey secret of your Alibaba Cloud account.You can view the AccessKey secret on the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page. |
        |Require SSL|Specifies whether to connect to Hologres by using SSL certificates. Do not select this check box.|

    3.  Click **Sign In**.

3.  Analyze data in a visualized manner by using Tableau Desktop.

    After Tableau Desktop is connected to Hologres, you can analyze data of existing tables in Hologres in a visualized manner. For more information, see [Tableau tutorial](https://www.tableau.com/zh-cn/learn/get-started).


## Publish workbooks of Tableau Desktop to Tableau Server

To publish a workbook of Tableau Desktop to Tableau Server for analysis and management, perform the following steps:

1.  Download and install Tableau Server.

    Visit the [Tableau official website](https://www.tableau.com/zh-cn/products) and download a Tableau Server client based on your business requirements. Install the client as prompted.

2.  Connect to Tableau Server by using Tableau Desktop.

    1.  Start Tableau Desktop and choose **Server** \> **Sign In** in the top navigation bar.

    2.  In the dialog box that appears, enter the endpoint of Tableau Server and click **Connect**.

        ![Sign In](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5445334161/p236676.png)

    3.  On the logon page, enter the username and password of Tableau Server and click **Sign In**.

3.  Publish a workbook to Tableau Server.

    1.  Log on to Tableau Server and choose **Server** \> **Publish Workbook** in the top navigation bar.

    2.  In the **Publish Workbook to Tableau Server** dialog box, set the parameters that are described in the following table.

        ![Publish Workbook to Tableau Server](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5445334161/p236693.png)

        |Parameter|Description|
        |---------|-----------|
        |Project|Enter the name of the project for which you want to publish a workbook.|
        |Name|Enter the name of the workbook that you want to publish.|
        |Tags|Tags help you find the related workbook when you browse the server. Separate tags with commas \(,\) or spaces.|
        |Permissions|The default value is applied.|
        |Data Sources|Tableau Server allows you to use the following two authentication methods for data sources:        -   Prompt user
        -   Embedded password
If you select Embedded password from the drop-down list, Tableau embeds the information about the connection between the workbook and the data source into the workbook and allows all users who are granted the required permissions to view the workbook. When you connect Tableau to Hologres, the AccessKey ID and AccessKey secret are required. We recommend that you select the embedded password mode.|

    3.  After you set the parameters, click **Publish**. Then, you can view the published workbook in Tableau Server.


