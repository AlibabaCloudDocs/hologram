---
keyword: [Grafana, Hologres, visualized data analysis]
---

# Grafana

This topic describes how to connect Grafana to Hologres and analyze data in a visualized manner.

1.  Install Grafana.

    For more information about how to install Grafana, see [Grafana official documentation](https://grafana.com/docs/grafana/latest/installation/?pg=docs).

2.  Connect Grafana to Hologres.

    1.  Log on to Grafana. In the left-side navigation pane, click the ![configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7961932261/p271368.png) icon and select **Data Sources**.

        ![Data Sources](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8961932261/p271370.png)

    2.  On the Data Sources tab, click **Add data source**.

        ![Add data source](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8961932261/p271376.png)

    3.  On the **Add data source** page, enter **PostgreSQL** in the search box at the top, find the PostgreSQL connection type among the matched results, and then click **Select**.

        ![select](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8961932261/p271387.png)

    4.  On the Settings tab, set the parameters in the PostgreSQL Connection section as required.

        ![Set parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8961932261/p271396.png)

        The following table describes the parameters.

        |Parameter|Description|
        |---------|-----------|
        |Host|The public endpoint of the Hologres instance to be connected to. You can view the public endpoint of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Database|The name of the Hologres database to be connected to.|
        |User|The AccessKey ID of your Alibaba Cloud account. For more information about how to obtain the AccessKey ID, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |Password|The AccessKey secret of your Alibaba Cloud account. For more information about how to obtain the AccessKey secret, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |SSL Mode|The mode in which the connection communicates with the Hologres instance. Set this parameter based on the SSL settings for the public endpoint of the Hologres instance.|
        |Version|The version of PostgreSQL. In this example, select 11.|
        |Other parameters|For more information about other parameters, see [Grafana official documentation](https://grafana.com/docs/grafana/latest/datasources/postgres/).|

    5.  After the parameters are set, click **Save & Test** in the lower part of the Settings tab. If the `Database Connection OK` message appears, the connection is valid.

3.  Analyze data.

    After the configuration is complete, you can analyze data based on the connection. For more information about data analysis, see [Grafana official documentation](https://grafana.com/docs/grafana/latest/getting-started/).

    ![Analyze data](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8961932261/p271454.png)


