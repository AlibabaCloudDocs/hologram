---
keyword: [Redash, Hologres, visualized data analysis]
---

# Redash

Redash is an open source business intelligence \(BI\) tool that is used for data visualization. It also provides features such as alerting and subscription. Compatible with PostgreSQL, Hologres allows you to connect your Hologres instance to Redash for data analysis. This topic describes how to connect Redash to Hologres and analyze Hologres data in Redash in a visualized manner.

## Procedure

1.  Install Redash.

    For more information, see the [Redash documentation](https://redash.io/help/open-source/setup).

2.  Connect Redash to your Hologres instance.

    1.  Log on to Redash. Click the ![Settings icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7793188161/p245041.png) icon in the upper-right corner and select Data Sources.

        ![data source](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7250887161/p248958.png)

    2.  On the **Data Sources** tab of the Settings page, click **New Data Source**.

        ![Create a connection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7250887161/p248963.png)

    3.  In the Create a New Data Source dialog box, select PostgreSQL in the Type Selection step, set the parameters as needed in the Configuration step, and then click **Create**. A connection is created. You can view the data of your Hologres instance in Redash.

        ![Create a connection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8250887161/p245060.png)

        |Step, parameter, or value|Description|
        |-------------------------|-----------|
        |Type Selection|PostgreSQL|When you select a type for the connection, search for PostgreSQL in the search box and click PostgreSQL.|
        |Configuration|Host|The public endpoint of your Hologres instance. You can view the public endpoint of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Port|The port number of your Hologres instance. You can view the port number of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |User|The AccessKey ID of your Alibaba Cloud account. For information about how to obtain the AccessKey pair, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |Password|The AccessKey secret of your Alibaba Cloud account. For information about how to obtain the AccessKey pair, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |Database Name|The name of the Hologres database to which you want to connect.|

3.  In the top navigation bar, choose **Create** \> **New Query**. On the New Query page, enter an SQL statement in the field in the right-side pane of the page.

    ![new query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8250887161/p245059.png)

4.  After the SQL statement is executed, you can create a visual chart based on the query results.

    ![Visual chart](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8250887161/p245062.png)

    For more information about how to write query statements and create visual charts in Redash, see the [Redash documentation](https://redash.io/help).


