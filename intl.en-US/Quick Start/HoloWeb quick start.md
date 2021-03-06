---
keyword: [HoloWeb, quick start]
---

# HoloWeb quick start

HoloWeb is a one-stop platform that is built based on Hologres. HoloWeb allows you to analyze data and manage databases in a visualized manner. This topic describes the basic operations that you can perform in HoloWeb.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  In the left-side navigation pane, click Instances. On the **Hologres Instances** page, click **Log on to Hologres Database**.

4.  Create a connection.

    1.  On the **Connection Management** page of the HoloWeb console, click the **Data Connection** tab. The New Connection dialog box appears.

        ![Data Connection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6398928061/p116500.png)

    2.  In the **New Connection** dialog box, set the parameters as required and click **OK**.

        ![New Connection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1790812161/p116502.png)

        The following table describes the parameters.

        |Parameter|Description|Required|
        |---------|-----------|--------|
        |Network type|        -   **public network**: Supported regions include China \(Shanghai\), China \(Shenzhen\), China \(Beijing\), China \(Hangzhou\), Singapore, China \(Hong Kong\), Malaysia \(Kuala Lumpur\), and US \(Silicon Valley\).
        -   **VPC**: You can select only the region from which you log on to the HoloWeb console.
|No|
        |Instance name|The Hologres instance that is created by using the current account.|No|
        |Connection name|If you have set the **Instance name** parameter, the value of the **Connection name** parameter is automatically set to the name of the specified instance. You can also enter a custom connection name.|Yes|
        |Connection description|The description of the connection.|No|
        |Host|The endpoint of the Hologres instance.You can view the endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

If you have set the **Instance name** parameter, the value of the **Host** parameter is automatically set to the endpoint of the specified instance. You can also enter the endpoint of the Hologres instance.

|Yes|
        |Port|The port number of the Hologres instance.You can view the port number of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

If you have set the **Instance name** parameter, the value of the **Port** parameter is automatically set to the port number of the specified instance. You can also enter the port number of the Hologres instance.

|Yes|
        |Logon Method|        -   **Password-free logon**: You can directly connect to the instance by using the current Alibaba Cloud account, without the need to enter the AccessKey ID and AccessKey secret.
        -   **Password logon**: You can enter the AccessKey ID and AccessKey secret of the current or other Alibaba Cloud account to connect to the instance.
|Yes|
        |AccessKey ID|The AccessKey ID of the current Alibaba Cloud account.You can obtain the AccessKey ID on the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.

If you set the Logon mode parameter to Password logon, you must set this parameter.

|No|
        |AccessKey Secret|The AccessKey secret of the current Alibaba Cloud account.You can obtain the AccessKey secret on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).

If you set the Logon mode parameter to Password logon, you must set this parameter.

|No|
        |Test connectivity|Tests whether the instance can be connected to.         -   If the instance can be connected to, the message **Test passed** is displayed.
        -   If the instance cannot be connected to, the message **Test failed** is displayed.
|No|

5.  Create a database.

    After you purchase a Hologres instance, a database named **postgres** is created by default. This database is allocated with fewer resources and used only for management purposes. We recommend that you create other databases to process business data. If you have created a database, skip this step and proceed to the next step.

    1.  On the **Connection Management** page of the HoloWeb console, click the **Database** tab. The New database dialog box appears.

        ![Database](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9405651161/p118032.png)

    2.  In the **New database** dialog box, set the parameters as required and click **OK**.

        ![DB](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0999438951/p118018.png)

        |Parameter|Description|
        |---------|-----------|
        |Connection name|The name of the connection based on which you want to create the database.|
        |Database name|The name of the database to be created. The database name must be unique.|
        |Permissions policy|The permission management model of the database. We recommend that you select Simple.|

6.  Create a query task.

    After you connect HoloWeb to a Hologres instance, you can use standard PostgreSQL statements to analyze data on the **SQL Editor** page. To create a query task, perform the following steps:

    1.  In the top navigation bar, click **SQL Editor**. On the Query page, click the **Ad-hoc Query** tab. The New SQL query dialog box appears.

    2.  In the **New SQL query** dialog box, set the parameters as required and click **OK**.

        |Parameter|Description|
        |---------|-----------|
        |Job Name|The name of the query task to be created.|
        |Directory|The directory where the query task is to reside. Default value: My SQL query.|
        |Connection name|The name of the connection based on which you want to create the query task.|
        |Database name|The name of the database from which data is to be queried.|

    3.  Enter the following statements in the SQL editor and click the ![r](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3749282061/p118141.png) icon:

        ```
        BEGIN;
        CREATE TABLE nation (
         n_nationkey bigint NOT NULL,
         n_name text NOT NULL,
         n_regionkey bigint NOT NULL,
         n_comment text NOT NULL,
        PRIMARY KEY (n_nationkey)
        );
        CALL SET_TABLE_PROPERTY('nation', 'bitmap_columns', 'n_nationkey,n_name,n_regionkey');
        CALL SET_TABLE_PROPERTY('nation', 'dictionary_encoding_columns', 'n_name,n_comment');
        CALL SET_TABLE_PROPERTY('nation', 'time_to_live_in_seconds', '31536000');
        COMMIT;
        
        INSERT INTO nation VALUES
        (11,'zRAQ', 4,'nic deposits boost atop the quickly final requests? quickly regula'),
        (22,'RUSSIA', 3  ,'requests against the platelets use never according to the quickly regular pint'),
        (2,'BRAZIL',  1 ,'y alongside of the pending deposits. carefully special packages are about the ironic forges. slyly special '),
        (5,'ETHIOPIA',  0 ,'ven packages wake quickly. regu'),
        (9,'INDONESIA', 2  ,'slyly express asymptotes. regular deposits haggle slyly. carefully ironic hockey players sleep blithely. carefull'),
        (14,'KENYA',  0  ,'pending excuses haggle furiously deposits. pending, express pinto beans wake fluffily past t'),
        (3,'CANADA',  1 ,'eas hang ironic, silent packages. slyly regular packages are furiously over the tithes. fluffily bold'),
        (4,'EGYPT', 4 ,'y above the carefully unusual theodolites. final dugouts are quickly across the furiously regular d'),
        (7,'GERMANY', 3 ,'l platelets. regular accounts x-ray: unusual, regular acco'),
        (20 ,'SAUDI ARABIA',  4 ,'ts. silent requests haggle. closely express packages sleep across the blithely');
        
        SELECT * FROM nation;
        ```

    4.  View the execution result.

7.  Create a foreign table.

    Seamlessly integrated with MaxCompute at the underlying layer, Hologres allows you to create foreign tables to accelerate queries of MaxCompute data. To create a foreign table, perform the following steps:

    1.  On the **Connection Management** \> **Accelerated Queries of MaxCompute Data** page of the HoloWeb console, click the **Create Foreign Table** tab. The New external table tab appears.

        ![o](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1790812161/p118150.png)

    2.  On the **New external table** tab, set the parameters as required and click **Submit**.

        ![c](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1790812161/p118151.png)

        |Parameter|Description|Remarks|
        |---------|-----------|-------|
        |Connection name|The name of the connection based on which you want to create the foreign table.|N/A|
        |Database|The name of the database where the foreign table is to be created.|N/A|
        |Table name|The name of the foreign table.|Enter a name as required.|
        |Description|The description of the foreign table.|Enter the description as required.|
        |Mode|The schema under which the foreign table is to be created.|Default value: **public**. You can also select a custom schema.|
        |Type|The service type of the source table.|The default value is MaxCompute, which cannot be changed.|
        |Server List|The server on which the source table is stored.|You can select the **odps\_server** server that is created at the underlying layer of Hologres. For more information, see [Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k).|
        |Table|The source table in MaxCompute to be mapped.|The value of this parameter is in the format of `project.table_name`. **Note:** You cannot map a MaxCompute table that is stored in a region different from that of the selected Hologres instance. |
        |SQL Script|The SQL editor where you can enter and edit SQL statements.|N/A|

    3.  After you create the foreign table, click **My connection** in the left-side navigation pane of the **Connection Management** page. Double-click the foreign table. On the tab that appears, click **Data preview** and preview the source data.

        ![Data preview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1790812161/p118166.png)


