# Dataiku

This topic describes how to connect Dataiku to Hologres. After Dataiku is connected to Hologres, you can analyze data in Hologres in a visualized manner.

## Limits

Dataiku allows you to directly use SQL Notebook rather than import tables to datasets to query foreign tables.

## Procedure

1.  Install Dataiku.

    Install Dataiku. For more information, see [Dataiku official documentation](https://www.dataiku.com/product/get-started/).

2.  Connect to Hologres.

    1.  Log on to Dataiku. Click **Blank project** and create a project. In this example, the project is named Hologres.

        ![Create a project](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9147038161/p254370.png)

    2.  On the project details page, click IMPORT YOUR FIRST DATASET to go to the Datasets tab.

        ![Import data](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3797838161/p254372.png)

    3.  On the Datasets tab, select **PostgreSQL** from the SQL dataset types. On the Connection tab, click **create a new connection**.

        ![Create a connection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0247038161/p254412.png)

    4.  Click **NEW CONNECTION** in the upper part of the Connections tab, select **PostgreSQL** from the drop-down list, and then create a connection.

        ![Create a connection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0247038161/p254414.png)

    5.  Set the parameters in the BASIC PARAMS section as required. You can also set the schema-related parameters if necessary.

        ![Set parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0247038161/p254432.png)

        The following table describes the parameters.

        |Parameter|Description|
        |---------|-----------|
        |New connection name|The name of the connection to be created.|
        |Host|The public endpoint of the Hologres instance. You can view the public endpoint of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Port|The port number of the Hologres instance. You can view the port number of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Database|The name of the Hologres database to be connected to.|
        |User|The AccessKey ID of your Alibaba Cloud account. For more information about how to obtain the AccessKey pair, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |Password|The AccessKey secret of your Alibaba Cloud account. For more information about how to obtain the AccessKey pair, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |Other parameters|For more information about other parameters, see [Dataiku official documentation](https://doc.dataiku.com/dss/latest/connecting/sql.html).|

    6.  After you set the required parameters, click **TEST** in the lower part of the page. After a success message appears, click **CREATE** to create the connection.

        ![Connection established](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0247038161/p254439.png)

3.  Analyze data.

    After settings are complete, click **Import tables to datasets** in the lower part of the page to import table schemas from the database to the dataset at a time. Then, you can analyze data. For more information about the operations of data analytics, see [Dataiku official documentation](https://doc.dataiku.com/dss/latest/connecting/sql.html).

    ![Data analysis](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0247038161/p254443.png)


