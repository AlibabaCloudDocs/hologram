---
keyword: [IBM Cognos Analytics, Hologres, visualized data analytics]
---

# IBM Cognos Analytics

IBM Cognos Analytics can access and process data with ease and implement data visualization. This gives you more confidence when you make decisions. This topic describes how to connect IBM Cognos Analytics to Hologres and analyze data in a visualized manner.

1.  Install IBM Cognos Analytics.

    Install IBM Cognos Analytics. For more information, see [IBM Cognos Analytics official documentation](https://www.ibm.com/docs/en/cognos-analytics/11.0.0?topic=started-getting-in-cognos-analytics).

2.  Connect IBM Cognos Analytics to Hologres.

    1.  Log on to IBM Cognos Analytics and click the ![manage](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1848890261/p264923.png) icon in the lower-left corner to create a connection.

    2.  Click the plus sign \(**+**\) in the upper-right corner and select PostgreSQL in the Select a type section.

        ![Create a connection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1848890261/p264945.png)

    3.  After you select PostgreSQL in the Select a type section, set the JDBC URL and Authentication method parameters on the page that appears.

        ![Parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2848890261/p264959.png)

        |Parameter|Description|
        |---------|-----------|
        |JDBC URL|The Java Database Connectivity \(JDBC\) URL that is used to connect to Hologres. Specify the value in the format of `jdbc:postgresql://<host>:<port>/<databasename>`. The JDBC URL contains the following variables:        -   host: the public domain name of the Hologres instance. You can view the domain name of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).
        -   port: the port number of the Hologres instance. You can view the port number of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).
        -   database: the name of the Hologres database. |
        |Driver class name|Use the default value for this parameter.|
        |Authentication method|Select **Use the following signon** for the Authentication method parameter.|

    4.  Click the plus sign \(**+**\) under the Authentication method parameter to add authentication information. On the Credentials tab, enter your user ID and password and enter the password again to confirm the password.

        ![User ID and password](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2848890261/p264997.png)

        |Parameter|Description|
        |---------|-----------|
        |User ID|The AccessKey ID of your Alibaba Cloud account. For information about how to obtain the AccessKey pair, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |Password|The AccessKey secret of your Alibaba Cloud account. For information about how to obtain the AccessKey pair, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|

    5.  After the configuration is complete, click **Test** in the lower-left part of the Settings tab. If the Success message appears, the test is successful. After you enter the name of the connection in the upper part of the page, click **Save**.

        ![Test](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2848890261/p265001.png)

    6.  After the connection configurations are saved, click the Schemas tab and select the schema for which you want to load the metadata. Click the More icon on the rightmost side of the schema and select **Load metadata** or **Load options**.

        ![schema](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2848890261/p265009.png)

        If you select **Load metadata**, the system loads the metadata of all the tables that reside in this schema. If you select **Load options**, you can specify the data to be loaded. In this example, select **Load metadata**.

    7.  After the required metadata in the schema is loaded, the system gives a prompt, as shown in the following figure. Then, you can use IBM Cognos Analytics to connect to Hologres and analyze data in a visualized manner.

        ![loaded](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2848890261/p265014.png)

3.  Create a data module.

    After the connection is created, you can create a data module for data analytics.

    1.  Log on to IBM Cognos Analytics, click the plus sign \(**+**\) in the lower-left corner, and then select Data module.

        ![data module](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2848890261/p265216.png)

    2.  On the Select sources page, click the ![data servers](../images/p265217.png) icon on the left side. Then, the Data servers tab appears. Click the connection that you have created.

        ![Select a dataset](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2848890261/p265220.png)

    3.  On the details page of the connection, the system displays the schemas in which the metadata has been loaded. You can select the schema from which you want to obtain data and click **OK** in the lower part of the page.

        ![Add a schema](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2848890261/p265221.png)

    4.  On the Add tables page, select **Select tables** and click **Next** in the lower part of the page.

    5.  On the Select tables page, select the name of the table from which you want to obtain data and click **OK**.

        ![select tables](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2848890261/p265230.png)

    6.  On the New data module page, click the Relationships tab and create a relationship between tables.

        ![relation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2848890261/p265246.png)

    7.  After you create the relationship between tables on the Create relationship page, click **Save** to save this module.

        ![Table relationship](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2848890261/p265271.png)

4.  After the data module is created, you can use this module for visualized data analytics. For more information about IBM Cognos Analytics, see [IBM Cognos Analytics official documentation](https://www.ibm.com/docs/en/cognos-analytics/11.0.0).

    ![Data analytics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2848890261/p265278.png)


