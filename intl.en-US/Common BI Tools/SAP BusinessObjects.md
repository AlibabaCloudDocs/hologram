---
keyword: [SAP BusinessObjects, Hologres, visualized data analytics]
---

# SAP BusinessObjects

SAP BusinessObjects \(SAP BO\) can provide the report, dashboard, ad hoc query, and data management features and is a famous business intelligence \(BI\) tool owned by SAP. This topic describes how to connect SAP BusinessObjects to Hologres and analyze data in a visualized manner.

1.  Install a PostgreSQL ODBC driver.

    You can download a PostgreSQL ODBC driver from [File Browser](https://www.postgresql.org/ftp/odbc/versions/msi/).

    **Note:** Hologres is compatible with the PostgreSQL 11 ecosystem. You must install the ODBC driver of the **psqlodbc\_11\_01\_0000 version** or a later version on the **SAP BusinessObjects server and client**.

2.  Create a relational connection.

    1.  Log on to Information Design Tool and choose **File** \> **New Universe**.

        ![new universe](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3319890261/p265851.png)

    2.  Select a project for which you want to create a connection from existing projects and click **Next**. If no project exists, select **Create a project** and create a project.

        ![project](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3319890261/p265854.png)

    3.  Select Relational data source and click **Next**.

        ![data source type](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1402421261/p265862.png)

    4.  Set the Resource Name parameter and click **Next**.

        ![Resource name](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3319890261/p266351.png)

    5.  Select Hierarchical List, choose **PostgreSQL** \> **PostgreSQL 11** \> **ODBC Drivers**, and then click **Next**.

        ![odbc](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3319890261/p266355.png)

    6.  Set the User Name and Password parameters. Select Use connection string, click the ![Refresh](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3319890261/p266361.png) icon next to the Driver field, select the driver that is used to connect SAP BusinessObjects to Hologres from the drop-down list, and then set other parameters. The following table describes the parameters.

        ![Set parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1402421261/p266358.png)

        |Parameter|Description|
        |---------|-----------|
        |User Name|The AccessKey ID of your Alibaba Cloud account. For information about how to obtain the AccessKey pair, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |Password|The AccessKey secret of your Alibaba Cloud account. For information about how to obtain the AccessKey pair, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |Host|The public domain name of the Hologres instance to be connected to. You can view the domain name of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Port|The port number of the Hologres instance to be connected to. You can view the port number of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Database|The name of the Hologres database to be connected to.|

    7.  Click **Test Connection**. If the database can be connected, the message Test Successful appears. Click **Next** to configure the connection and set the parameters. If no special configuration is required, click **Next**.

3.  Create a data foundation.

    1.  On the New Data Foundation page, set the Resource Name parameter and click **Next**.

    2.  Select the tables from which you want to obtain data and click **Next**.

        ![Select tables](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3319890261/p266409.png)

4.  Create a relational business layer.

    On the New Relational Business Layer page, set the Resource Name parameter and click **Finish**. Then, Hologres is connected to SAP BusinessObjects and you can design a model to analyze data in a visualized manner.


