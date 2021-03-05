---
keyword: [Hologres, Apache Superset, visualized data analysis]
---

# Apache Superset

This topic describes how to connect Apache Superset to Hologres and analyze data in a visualized manner.

-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).
-   Apache Superset is installed. For more information, visit the [Apache Superset official website](https://superset.apache.org/resources).

Apache Superset \(incubating\) is an open source BI tool that is used for data exploration and visualized data analysis. Apache Superset is developed based on Python by using components such as Flask, Pandas, and SQLAlchemy. Hologres is compatible with PostgreSQL. In this example, Apache Superset version 0.38.0 is connected to Hologres for data analysis.

1.  Log on to Apache Superset and choose **Sources** \> **Databases** in the top navigation bar. Then, the Databases page appears.

    ![databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6437584161/p224572.png)

2.  On the Databases page, click the ![Add](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6437584161/p224576.png) icon in the upper-right corner. On the Add Database page, set the parameters as required.

    ![Create a connection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6437584161/p224628.png)

    |Parameter|Description|
    |---------|-----------|
    |Database|The custom name of the connection.|
    |SQLAlchemy URI|The connection string that is used to connect to the Hologres database. An SQLAlchemy URI is in the following format:    ```
postgresql+psycopg2://{AccessKey ID}:{AccessKey Secret}@{host}:{port}/{database}
    ```

An SQLAlchemy URI contains the following parameters:    -   AccessKey ID and AccessKey Secret: the AccessKey ID and AccessKey secret of your Alibaba Cloud account. For more information about how to obtain the AccessKey pair, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).
    -   host: the public endpoint of the Hologres instance. You can view the public endpoint of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).
    -   port: the port number of the Hologres instance. You can view the port number of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).
    -   database: the name of the Hologres database. You can view the database name on the Databases tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).
For more information about the connection information, see [Engine Configuration](https://docs.sqlalchemy.org/en/12/core/engines.html#postgresql) in the SQLAlchemy documentation.|

3.  Click **Test Connection** to check whether the connection is established and whether the settings of the connection are valid.

4.  If the connection is successful, click **Save** in the lower part of the page.

    After the configurations are saved, you can connect Apache Superset to Hologres to analyze and display data in a visualized manner. For more information, see [Postgres](https://superset.apache.org/docs/databases/postgres) in the Apache Superset documentation.


