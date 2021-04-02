---
keyword: [Hologres, basic process, quick start, PostgreSQL client]
---

# PSQL quick start

This topic describes how to connect to and use Hologres from the PostgreSQL client. You will have a better understanding of the basic process of using Hologres after you read this topic.

Hologres is compatible with PostgreSQL and integrates seamlessly with the big data ecosystem. Hologres allows you to directly query and analyze MaxCompute data. You can also use Hologres to insert and query a large amount of data in real time at a high concurrency. Hologres helps you construct a real-time data warehouse for your enterprise with high efficiency.

The following content describes the basic process of using Hologres from the PostgreSQL client.

You can also get started with Hologres in a visualized manner by using HoloWeb.

1.  Purchase a Hologres instance.

    1.  Log on to the [Alibaba Cloud international site \(alibabacloud.com\)](https://www.alibabacloud.com/).

    2.  Go to the Hologres [product landing page](https://www.alibabacloud.com/zh/product/hologres) and purchase a Hologres instance. For more information, see [Billing methods](/intl.en-US/Pricing/Billing methods.md).

2.  View the instance details in the Hologres console.

    1.  Go to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

    2.  In the left-side navigation pane, click Instances. On the **Hologres Instances** page, click the name of the purchased instance. On the instance details page that appears, view the instance details.

        You can also click **Manage** in the **Actions** column of the target instance to go to the instance details page.

3.  Connect to the Hologres instance from the PostgreSQL client. For more information, see [Connect to a Hologres instance from the PostgreSQL client](/intl.en-US/Common Development Tools/Connect to a Hologres instance from the PostgreSQL client.md).

4.  Create a database for your business purposes.

    After you purchase a Hologres instance, a database named **postgres** is created by default. This database is allocated with a few resources and only used for management purposes. We recommend that you create a database based on your business needs.

    For example, you can execute the following SQL statement to create a database:

    ```
    CREATE Database NewDatabaseName;
    CREATE Database test; // Create a database named test.
    ```

5.  Authorize a RAM user.

    1.  Create a RAM user. For more information, see [Create a RAM user](/intl.en-US/Preparations/Use Hologres as a RAM user.md). If you have created a RAM user, skip this step.

    2.  Grant the development permissions on the created Hologres instance to the RAM user. For more information, see [Grant the development permissions on a Hologres instance to RAM users](/intl.en-US/User Authorization/Authorization operations/Grant the development permissions on a Hologres instance to RAM users.md).

        If the RAM user is assigned the normal user role, you can use the RAM user to access the Hologres instance only after the development permissions on the instance are granted to the RAM user. If the RAM user is assigned the superuser role, skip this step.

6.  Analyze data.

    Use standard PostgreSQL statements to analyze data from the PostgreSQL client.

    For example, you can execute the following SQL statements to create a table in the database and write data to the table:

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

    You can also use HoloWeb for data analytics. For more information, see [HoloWeb quick start](/intl.en-US/Quick Start/HoloWeb quick start.md) .

    For more information about data analytics in Hologres, see [Create a foreign table in Hologres to accelerate queries of MaxCompute data](/intl.en-US/Data Access/Batch synchronization/MaxCompute/Create a foreign table to accelerate queries of MaxCompute data.md) and [Write data from Realtime Compute to Hologres in real time]().


