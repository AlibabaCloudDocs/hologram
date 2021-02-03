---
keyword: [Hologres, real-time analysis, best practice]
---

# Analyze large amounts of MaxCompute data in real time

This topic describes how to query large amounts of MaxCompute data and analyze and display the query results in a visualized manner.

-   MaxCompute is activated. For more information, see [Activate MaxCompute](/intl.en-US/Prepare/Activate MaxCompute.md).

    **Note:** Make sure that you activate the MaxCompute and Hologres services in the same region.

-   A Hologres instance is purchased and connected to HoloWeb. For more information, see Quick start to HoloWeb.
-   Quick BI is activated. For more information, see [Prerequisites]().

Hologres is a real-time interactive analytics engine. It is compatible with PostgreSQL and integrates seamlessly with MaxCompute.

You can create a foreign table in Hologres to directly query data in MaxCompute.

This topic uses a Taobao store as an example to describe how to create a customer persona with the following information: the regional distribution and age composition of customers, the number of preferred customers, and the regional distribution of preferred customers who were born between 1980 and 1990.

The complete link for speeding up the query of maxcompute data using hologres is shown below.

1.  Save data of customers who visited the store to MaxCompute tables.
2.  Create a foreign table in Hologres to directly query data in MaxCompute.
3.  Connect Quick BI to the target Hologres instance to display the customer persona in a visualized manner.

1.  Prepare a MaxCompute data source.

    Create a table in MaxCompute and import data to the table. For more information, see [Create and view a table](/intl.en-US/Quick Start/Create and view a table.md).

    This best practice uses the following existing tables in the MaxCompute project **public\_data**.

    |MaxCompute table|Data entries|
    |----------------|------------|
    |customer|12 million|
    |customer\_address|6 million|
    |customer\_demographics|1.92 million|

2.  Create a foreign table in Hologres and query the table.

    To use [HoloWeb](https://holoweb-cn-shanghai.data.aliyun.com/connect) to create a foreign table in Hologres for accelerating data queries from MaxCompute, perform the following steps:

    1.  Connect a Hologres instance to HoloWeb.

        Log on to [HoloWeb](https://holoweb-cn-shanghai.data.aliyun.com/connect). On the **Connection Management** tab, click **Data Connection**. In the **New Connection** dialog box, set parameters as required and click **OK**.

        ![New Connection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1790812161/p116502.png)

        |Parameter|Description|Remarks|
        |---------|-----------|-------|
        |Connection name|The name of the connection. Enter a name as required.|None|
        |Connection description|The description of the connection.|None|
        |Host|The public endpoint of the Hologres instance.|You can view the public endpoint of the Hologres instance on the **Configuration** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Port|The public port number of the Hologres instance.|You can view the port number of the Hologres instance on the **Configuration** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Initialize database|The name of the Hologres database to be connected to HoloWeb.|NoneYou can view databases in a Hologres instance on the **Databases** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
        |User name|The AccessKey ID of the current Alibaba Cloud account.|You can obtain the AccessKey ID in the [User Management console](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).|
        |Password|The AccessKey secret of the current Alibaba Cloud account.|You can obtain the AccessKey secret in the [User Management console](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).|
        |Test connectivity|Check whether the data connection is successful.         -   Successful: The **"Test passed"** message appears.
        -   Failed: The **"Test failed"** message appears.
|None|

    2.  Create a foreign table.

        Click **Connection Management** in the top navigation bar. On the Connection Management page, click **External Table**. On the New external table tab, set parameters as required to create a foreign table in a visualized manner.

        Enter the name of the MaxCompute table to be queried. Then, fields in the table appear. Select fields to be synchronized and click **Submit**.

        **Note:**

        -   Hologres does not support querying MaxCompute tables that reside in a different region from the current Hologres instance.
        -   A server is required for storing a foreign table. You can directly call the **odps\_server** server created at the underlying layer of Hologres. For more information, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k).
        You can use the following SQL statement to create multiple foreign tables at a time:

        ```
        IMPORT FOREIGN SCHEMA public_data LIMIT to(
          customer,
          customer_address,
          customer_demographics,
          inventory,item,
          date_dim,
          warehouse) 
          FROM server odps_server INTO PUBLIC options(if_table_exist 'update');
        ```

    3.  Preview a foreign table.

        After you create a foreign table, click **My Connections** on the **Connection Management** tab.

        Right-click the target foreign table and click **Data Preview** to view data in the MaxCompute table mapped to the foreign table.

        The **Data Preview** tab only shows partial data in the foreign table.

    4.  Query data in the foreign table.

        Click Query in the top navigation bar. On the page that appears, click SQL Window. In the New SQL Query dialog box, set parameters as required. Select the connection and database to which the target foreign table belongs. In the SQL editor, write an SQL statement to query data as required.

        For example, you can use the following SQL statements:

        ```
        # SQL 1: Query the number of non-preferred customers and the numbers of preferred customers with various flags, and sort the query results in descending order of the number of customers.
        SELECT c_preferred_cust_flag,
               count(*) AS cnt
        FROM customer
        WHERE c_preferred_cust_flag IS NOT NULL
        GROUP BY c_preferred_cust_flag
        ORDER BY cnt DESC LIMIT 10;
        
        # SQL 2: Query the number of customers born in each year, and display the years in which more than 1,000 customers were born in descending order of the number of customers.
        SELECT c_birth_year,
               count(*) AS cnt
        FROM customer
        WHERE c_birth_year IS NOT NULL
        GROUP BY c_birth_year HAVING count(*) > 1000
        ORDER BY cnt DESC LIMIT 10;
        
        # SQL 3: Query the number of customers in each city, and display the cities where more than 10 customers reside in descending order of the number of customers.
        SELECT ca_city,
               count(*) AS cnt
        FROM customer ,
             customer_address
        WHERE c_current_addr_sk = ca_address_sk
          AND ca_city IS NOT NULL
        GROUP BY ca_city HAVING count(*) > 10
        ORDER BY cnt DESC LIMIT 10;
        
        # SQL 4: Query the number of customers who were born between 1980 and 1990 in each city, and display the cities in which more than 10 customers born between 1980 and 1990 reside in descending order of the number of customers.
        SELECT ca_city,
               count(*) AS cnt
        FROM customer ,
             customer_address
        WHERE c_current_addr_sk = ca_address_sk
          AND c_birth_year >= 1980
          AND c_birth_year < 1990
          AND c_preferred_cust_flag = 'Y'
          AND ca_city IS NOT NULL
        GROUP BY ca_city HAVING count(*) > 10
        ORDER BY cnt DESC LIMIT 10;
        ```

3.  Use Quick BI to analyze data.

    To connect Quick BI to the target Hologres instance to analyze and display data queried from MaxCompute in a visualized manner, perform the following steps:

    1.  Add a connection.

        Log on to the Quick BI console and add a PostgreSQL connection to Hologres. For more information, see [Quick BI](/intl.en-US/Common BI Tools/Quick BI.md).

    2.  Create a dataset.

        After you connect Quick BI to Hologres, create a dataset and import the required data to the dataset to produce reports.

        This best practice uses **ad hoc query SQL statements** to create a dataset.

    3.  Display the customer persona in a visualized manner.


