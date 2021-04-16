---
keyword: [Hologres, Apache Zeppelin, visualized data analytics]
---

# Apache Zeppelin

Apache Zeppelin is a web-based notebook that enables interactive data analytics. Apache Zeppelin is integrated with abundant language backends or interpreters, such as Scala, Python, Spark SQL, and Hive. This helps you create interactive, collaborative documents. Hologres is compatible with PostgreSQL. It allows you to directly connect to Apache Zeppelin for data analytics. This topic shows you how to connect Apache Zeppelin to Hologres and analyze data in a visualized manner.

## Limits

In the latest version of Apache Zeppelin, the PostgreSQL interpreter is deprecated and its features are merged into the JDBC interpreter. In this case, you can use the JDBC interpreter that provides the same features to connect Apache Zeppelin to Hologres.

## Connect Apache Zeppelin to Hologres

1.  Install Apache Zeppelin.

    For more information about how to install Apache Zeppelin, see [Apache Zeppelin official documentation](http://zeppelin.apache.org/docs/latest/quickstart/install.html).

2.  Configure an interpreter.

    1.  Log on to Apache Zeppelin, click the username in the upper-right corner, and then select Interpreter from the drop-down list.

        ![Interpreter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5841458161/p244967.png)

    2.  Click **Create** in the upper-right corner and set the required parameters to create an interpreter.

        ![Interpreter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5841458161/p244976.png)

        -   Enter a name in the Interpreter Name field.
        -   Select **JDBC** from the Interpreter group drop-down list.
    3.  After you select **JDBC** from the Interpreter group drop-down list, set the parameters described in the following table.

        ![Configure an interpreter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5841458161/p244983.png)

        |Parameter|Description|
        |---------|-----------|
        |default.url|The Java Database Connectivity \(JDBC\) URL that is used to connect to Hologres. Specify the value in the format of `jdbc:postgresql://{host}:{port}/{database}`. The JDBC URL contains the following variables:        -   host: the address of the Hologres instance. You can view the address of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).
        -   port: the port number of the Hologres instance. You can view the port number of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).
        -   database: the name of the Hologres database. |
        |default.user|The AccessKey ID of your Alibaba Cloud account. For more information about how to obtain the AccessKey ID, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |default.password|The AccessKey secret of your Alibaba Cloud account. For more information about how to obtain the AccessKey secret, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|

        You can also set other parameters on the Properties page based on your business needs. For more information about the parameters, see [Apache Zeppelin official documentation](http://zeppelin.apache.org/docs/latest/interpreter/jdbc.html).

    4.  Click **Save** in the lower part of the page. Then, the configuration is saved.

3.  Create a notebook for data analytics.

    You must add the name of the interpreter, such as `%hologres`, in front of the SQL statement that you are writing in the notebook. This way, Apache Zeppelin can use the specified interpreter to generate and send a query request. You can specify the way to display data on the page. The query result is displayed in the lower part of the page.

    ![notebook](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5841458161/p244992.png)


