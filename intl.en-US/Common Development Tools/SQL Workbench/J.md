---
keyword: [SQL Workbench/J, Hologres, development tool]
---

# SQL Workbench/J

This topic describes how to connect SQL Workbench/J to Hologres. After SQL Workbench/J is connected to Hologres, you can use it to analyze data in Hologres in a visualized manner.

-   SQL Workbench/J is downloaded from the [SQL Workbench/J](https://www.sql-workbench.eu/downloads.html) official website and is installed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

SQL Workbench/J is a free, cross-platform SQL query and analysis tool. You can use a PostgreSQL driver to connect SQL Workbench/J to Hologres.

1.  Start SQL Workbench/J and establish a connection to Hologres.

2.  Configure the parameters listed in the following table.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5231013061/p65594.png)

    |Parameter|Description|Example value|
    |---------|-----------|-------------|
    |Driver|PostgreSQLUsed to connect to Hologres.

|None|
    |URL|`jdbc:postgresql://endpoint:port/database`    -   Endpoint: the public endpoint of the Hologres instance.

You can view the public endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

    -   Port: the public port number of the Hologres instance.

You can view the public port number of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

    -   Database: the name of the Hologres database to which you want to connect.
|`jdbc:postgresql://holodemo-cn-hangzhou.aliyun.com:80/postgres`This value is only for reference. |
    |Username|The AccessKey ID of your account that is used to access the Hologres instance.You can obtain the AccessKey ID on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).

|None|
    |Password|The AccessKey secret of your account that is used to access the Hologres instance.You can obtain the AccessKey secret on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).

|None|

3.  Configure extended properties.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9484903061/p65608.png)

    1.  Click **Extended Properties**. In the Edit extended properties dialog box, set SSL to true.

    2.  Click **OK**.

4.  Click **OK**. SQL Workbench/J is connected to Hologres.

    You can query and analyze data of the Hologres instance in SQL Workbench/J. For more information about how to configure the driver and install SQL Workbench/J, see the [SQL Workbench/J documentation](https://www.sql-workbench.eu/getting-started.html) on the official website.


