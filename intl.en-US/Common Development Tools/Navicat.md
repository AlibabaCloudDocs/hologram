---
keyword: [Navicat, connect to Hologres, data development]
---

# Navicat

Navicat is a multi-connection database development tool that allows you to connect multiple databases within a single application. It helps you quickly create, manage, and maintain databases. This topic describes how to connect Navicat to Hologres for data development.

-   A Navicat client is downloaded from the [Navicat official website](https://www.navicat.com.cn/products) and is installed. In this example, Navicat 15 for PostgreSQL is downloaded and installed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

1.  Establish a connection to Hologres.

    Start Navicat 15 for PostgreSQL. Choose **Connection** \> **PostgreSQL** in the top navigation bar.

2.  On the Common tab of the **PostgreSQL - New Connection** dialog box, configure the parameters listed in the following table.

    |Parameter|Description|Example value|
    |---------|-----------|-------------|
    |Connection Name|The custom name of the connection.|None|
    |Host|The public endpoint of the Hologres instance.You can view the public endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

|holodemo-cn-hangzhou.hologres.aliyuncs.com|
    |Port|The public port number of the Hologres instance.You can view the public port number of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

|80|
    |Initial Database|The name of the Hologres database to which you want to connect.|postgres|
    |User Name|The AccessKey ID of your account that is used to access the Hologres instance.You can obtain the AccessKey ID on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).

|None|
    |Password|The AccessKey secret of your account that is used to access the Hologres instance.You can obtain the AccessKey secret on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).

|None|
    |Test Connection|If the **Connection succeeded** message appears after you click **Test Connection**, Navicat 15 for PostgreSQL is connected to Hologres.|None|

3.  Click **OK**.

4.  Develop data in Navicat 15 for PostgreSQL.

    After Navicat 15 for PostgreSQL is connected to Hologres, you can develop data in Navicat 15 for PostgreSQL. For more information, see [Navicat documentation](https://www.navicat.com.cn/manual/online_manual/cn/navicat/mac_manual/#/postgresql_database). Typical use scenarios:

    -   Accelerated query of offline data: For more information, see [Create a foreign table in Hologres to accelerate queries of MaxCompute data](/intl.en-US/Data Access/Batch synchronization/MaxCompute/Create a foreign table to accelerate queries of MaxCompute data.md).

