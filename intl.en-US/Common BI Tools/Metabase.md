---
keyword: [Metabase, Hologres, visualized data analytics]
---

# Metabase

Metabase is an open source business intelligence \(BI\) tool that allows you to understand and analyze data, or query data to obtain formatted results such as graphical views. This helps you make decisions based on data. Hologres is compatible with PostgreSQL. Hologres allows you to connect to Metabase for data analytics. This topic describes how to connect Metabase to Hologres. After Metabase is connected to Hologres, you can analyze data in Hologres in a visualized manner.

## Procedure

1.  Install Metabase.

    Install Metabase. For more information, see [Metabase Documentation](https://www.metabase.com/docs/latest/operations-guide/installing-metabase.html).

2.  Connect to Hologres.

    1.  Log on to Metabase. In the upper-right corner of the top navigation bar, click the ![Settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1504188161/p254220.png) icon and select **Admin** from the shortcut menu.

        ![Administrator](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6147038161/p254226.png)

    2.  On the Metabase Admin page, click Database in the top navigation bar. On the Database tab, click **Add database** in the upper-right corner and create a connection to Hologres.

        ![Add a database](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6147038161/p254229.png)

    3.  After you select the type of a database to be connected and set relevant parameters, click **Save**.

        ![Set parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6147038161/p254238.png)

        The following table describes the parameters.

        |Parameter|Description|
        |---------|-----------|
        |Database type|The type of the database to be connected. Select **PostgreSQL** from the drop-down list.|
        |Host|The public endpoint of the Hologres instance. You can view the public endpoint of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Port|The port number of the Hologres instance. You can view the port number of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Database name|The name of the Hologres database to be connected to.|
        |Database username|The AccessKey ID of your Alibaba Cloud account. For more information about how to obtain the AccessKey pair, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |Database password|The AccessKey secret of your Alibaba Cloud account. For more information about how to obtain the AccessKey pair, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |Other parameters|For more information about other parameters, see [Metabase Documentation](https://www.metabase.com/docs/latest/administration-guide/01-managing-databases.html).|

3.  Analyze data.

    After settings are complete, you can connect Metabase to Hologres for data analytics. For more information about the operations of data analytics, see [Metabase Documentation](https://www.metabase.com/docs/latest/).


