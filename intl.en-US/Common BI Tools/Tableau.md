---
keyword: [BI tool, visualized data analysis, Tableau]
---

# Tableau

This topic describes how to connect Tableau to Hologres. After Tableau is connected to Hologres, you can analyze data in Hologres in a visualized manner.

Tableau is a secure and flexible end-to-end data analysis platform. It provides a complete set of features from connection setup to collaboration. Hologres is compatible with PostgreSQL. You can connect Tableau to Hologres for data analysis in a visualized manner.

1.  Download and install Tableau.

    Visit the [Tableau official website](https://www.tableau.com/zh-cn/products) and download a Tableau client based on your business requirements. Install the client as prompted. In this example, download and install [Tableau Desktop](https://www.tableau.com/zh-cn/products/desktop).

2.  Establish a connection to Hologres.

    1.  Start Tableau Desktop.

    2.  In the left-side navigation pane, choose **Connection** \> **To Server** \> **PostgreSQL**. In the PostgreSQL dialog box, configure the parameters listed in the following table for a connection to Hologres.

        |Parameter|Description|
        |---------|-----------|
        |Server|The public endpoint of the Hologres instance.You can view the public endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
        |Port|The port number of the Hologres instance.You can view the port number of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
        |Database|The name of the Hologres database.|
        |Authentication|The authentication method. Select **Username and Password**.|
        |Username|The AccessKey ID of your Alibaba Cloud account.You can obtain the AccessKey ID on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak). |
        |Password|The AccessKey secret of your Alibaba Cloud account.You can obtain the AccessKey secret on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak). |
        |Require SSL|Specifies whether to connect to Hologres by using SSL. Do not select this check box.|

    3.  Click **Logon**.

3.  Analyze data in a visualized manner by using Tableau Desktop.

    After Tableau Desktop is connected to Hologres, you can analyze data of existing tables in Hologres in a visualized manner. For more information, see [Tableau tutorial](https://www.tableau.com/zh-cn/learn/get-started).


