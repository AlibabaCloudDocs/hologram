---
keyword: [DataService Studio, Hologres, API, BI tool]
---

# DataWorks DataService Studio

This topic describes how to connect DataWorks DataService Studio to Hologres and how to use DataWorks DataService Studio to create an API.

-   DataWorks is activated. For more information, see [Overview]().
-   A Hologres instance is purchased and bound to a DataWorks workspace. For more information, see [Quick start to HoloStudio](/intl.en-US/HoloStudio/Quick start to HoloStudio.md).

DataService Studio is designed to build a unified data service bus to help you manage APIs. You can create APIs from tables or register existing APIs with DataService Studio. This helps you manage and publish APIs in a centralized manner.

Hologres is deeply integrated with DataWorks and directly connects to DataService Studio. You can quickly create APIs based on data query results and use these APIs to provision services.

1.  Establish a connection to Hologres.

    1.  Log on to the [DataWorks console](https://workbench.data.aliyun.com/console?#/).

    2.  In the left-side navigation pane, click **Workspaces**.

    3.  In the top navigation bar, select the region where your workspace resides. Find your workspace and click **Data Integration** in the Actions column.

    4.  On the page that appears, click **Connection** in the left-side navigation pane.

    5.  In the upper-right corner of the **Data Source** page, click **New data source**.

    6.  Select **Hologres** in the **Big Data Storage** section.

    7.  In the **Add Hologres data source** dialog box, configure the parameters listed in the following table.

        ![Add Hologres data source](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4073560061/p139621.png)

        |Parameter|Description|
        |---------|-----------|
        |Data source type|The type of the data source. Only **Alibaba Cloud instance mode** is supported.|
        |Data Source Name|The name of the data source. The name must contain letters, digits, and underscores \(\_\) and must start with a letter.|
        |Description|The description of the data source. The description cannot exceed 80 characters in length.|
        |Applicable environment|        -   **Development**
        -   **Production**
**Note:** If your workspace is created in standard mode, this parameter is required. |
        |Instance ID|The ID of the Hologres instance from which you want to synchronize data.You can query the ID of the Hologres instance in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
        |Database Name|The name of the database from which data is queried.|
        |AccessKey ID|You can obtain the AccessKey ID on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).|
        |AccessKey Secret|You can obtain the AccessKey secret on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).|
        |Resource Group connectivity|The resource group that is used to synchronize data. Select **Data Service**.If your data integration tasks use this data source, make sure that the public resource group can access the data source. |

2.  Create an API.

    1.  On the **DataWorks** development page, click the ![Icon](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8421821061/p139848.png) icon in the top navigation bar.

    2.  Move the pointer over **All Products**. In the **Data Development** section, click **DataService Studio**.

    3.  On the **Service Development** page, move the pointer over the ![Create](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1990723061/p140098.png) icon.

    4.  Choose API \> **Generate API**. In the Generate API dialog box, you can select **Wizard Mode** or **Script Mode** to create an API.

        ![Create an API](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0621013061/p140103.png)

        For more information about how to create an API in **wizard mode**, see [Create an API in the codeless UI]().

        For more information about how to create an API in **script mode**, see [Create an API in the code editor]().

3.  Test the API.

    1.  On the configuration tab of the API, click **Test** in the upper-right corner.

    2.  In the **Test APIs** dialog box, specify the values in **Request Parameters** and click **Test**.

        ![Test APIs](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0621013061/p140167.png)

        If the **Tested successfully** message appears in the lower part of the **Test APIs** dialog box, the API passes the test.

        You can also test the API in the Test APIs module of DataService Studio. For more information, see [Test an API]().

4.  Publish and view the API.

    1.  On the configuration tab of the API, click **Publish** in the upper-right corner.

        Publish the API to API Gateway and make the API available on the API marketplace. For more information, see [Publish an API]().

    2.  On the **DataService Studio** page, click **Service Management** on the right side of the top navigation bar.

    3.  Find the published API and click the API name to view the details.

5.  Call the API.

    For more information about how to call a published API, see Example of client calling API.


