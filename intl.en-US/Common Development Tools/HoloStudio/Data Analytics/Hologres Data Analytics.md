---
keyword: [periodic scheduling, Hologres]
---

# Hologres Data Analytics

HoloStudio is seamlessly integrated with DataWorks. You can use HoloStudio to import data from MaxCompute to Hologres. You can also use DataWorks to schedule nodes to periodically import data to Hologres. This topic describes how to import data from a MaxCompute source table to Hologres for periodic scheduling.

1.  Prepare a MaxCompute table.

    Prepare a MaxCompute table that stores source data. You can create a table and import data, or directly select a table from the data map. For more information, see [t11950.dita\#concept\_rkk\_kcy\_5db](/intl.en-US/Quick Start/Create and view a table.md). The following sample statements use a table created in the data map:

    ```
    CREATE TABLE IF NOT EXISTS bank_data_odps
    (
     age             BIGINT COMMENT 'Age',
     job             STRING COMMENT 'Job type',
     marital         STRING COMMENT 'Marital status',
     education       STRING COMMENT 'Education level',
     card         STRING COMMENT 'Credit card available or not',
     housing         STRING COMMENT 'Mortgage',
     loan            STRING COMMENT 'Loan',
     contact         STRING COMMENT 'Contact',
     month           STRING COMMENT 'Month',
     day_of_week     STRING COMMENT 'Day of the week',
     duration        STRING COMMENT 'Duration',
     campaign        BIGINT COMMENT 'Number of contacts during the campaign',
     pdays           DOUBLE COMMENT 'Interval from the last contact',
     previous        DOUBLE COMMENT 'Number of contacts with the customer',
     poutcome        STRING COMMENT 'Result of the previous marketing campaign',
     emp_var_rate    DOUBLE COMMENT 'Employment change rate',
     cons_price_idx  DOUBLE COMMENT 'Consumer price index',
     cons_conf_idx   DOUBLE COMMENT 'Consumer confidence index',
     euribor3m       DOUBLE COMMENT 'Euro deposit rate',
     nr_employed     DOUBLE COMMENT 'Number of employees',
     y               BIGINT COMMENT 'Time deposit available or not'
    );
    ```

2.  Create a foreign table.

    On the HoloStudio page, click PG management in the left-side navigation pane. Move the pointer over the Create icon and click External Table. On the page that appears, create a foreign table that maps to the MaxCompute table. The following SQL statements provide an example:

    ```
    begin;
    CREATE FOREIGN TABLE "public"."bank_data_foreign_holo" (
     "age" int8,
     "job" text,
     "marital" text,
     "education" text,
     "card" text,
     "housing" text,
     "loan" text,
     "contact" text,
     "month" text,
     "day_of_week" text,
     "duration" text,
     "campaign" int8,
     "pdays" float8,
     "previous" float8,
     "poutcome" text,
     "emp_var_rate" float8,
     "cons_price_idx" float8,
     "cons_conf_idx" float8,
     "euribor3m" float8,
     "nr_employed" float8,
     "y" int8
    )
    SERVER odps_server
    OPTIONS (project_name 'odps_4_holoworkshop_dev', table_name 'bank_data_odps');
    COMMIT;
    ```

    The following table describes the parameters of OPTIONS.

    |Parameter|Description|
    |---------|-----------|
    |project\_name|The name of the MaxCompute project where the tables for which you want to create foreign tables reside.|
    |table\_name|The name of the MaxCompute table.|

3.  Create a data storage table.

    On the HoloStudio page, click PG management or SQL Console in the left-side navigation pane. On the page that appears, create a table for storing data. The fields in this table must be of the same data types as those in the foreign table. The following examples show how to use DDL statements to create HBase tables:

    ```
    begin;
    create table if not exists bank_data_holo (
     age int8,
     job text,
     marital text,
     education text,
     card text,
     housing text,
     loan text,
     contact text,
     month text,
     day_of_week text,
     duration text,
     campaign int8,
     pdays float8,
     previous float8,
     poutcome text,
     emp_var_rate float8,
     cons_price_idx float8,
     cons_conf_idx float8,
     euribor3m float8,
     nr_employed float8,
     y int8,
     ds text not null
    )
    partition by list(ds);
    call set_table_property('bank_data_holo', 'orientation', 'column');
    call set_table_property('bank_data_holo', 'time_to_live_in_seconds', '700000');
    commit;
    ```

4.  Create a partitioned table.

    1.  On the HoloStudio page, click the ![Develop data in HoloStudio](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1397196061/p178952.png) icon in the left-side navigation pane.

    2.  On the Data Analytics page, move the pointer over the ![Create icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1397196061/p178953.png) icon and click **Interactive Analytics Development** to create a data analytics node.

    3.  In the **Create Node** dialog box, set **Node Name**, **location**, and **Database**.

        ![Create Node dialog box](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1397196061/p179024.png)

    4.  Click **Commit**.

    5.  On the editing page of the node, enter the following statements:

        ```
        create table if not exists bank_data_holo_1_${bizdate} partition of bank_data_holo
          for values in ('${bizdate}');
        
        insert into bank_data_holo_1_${bizdate}
        select 
            age as age,
            job as job,
            marital as marital,
            education as education,
            card as card,
             housing as housing,
            loan as loan,
            contact as contact,
            month as month,
            day_of_week as day_of_week,
             duration as duration,
            campaign as campaign,
             pdays as pdays,
            previous as previous,
            poutcome as poutcome,
             emp_var_rate as emp_var_rate,
            cons_price_idx as cons_price_idx,
            cons_conf_idx as cons_conf_idx,
            euribor3m as euribor3m,
            nr_employed as nr_employed,
            y as y,
            '${bizdate}' as ds 
        from bank_data_foreign_holo;
        ```

    6.  Click the ![Save icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1397196061/p179028.png) icon in the top navigation bar.

    7.  Click **Go to DataStudio for Scheduling** to go to the DataStudio page.

        ![Click Go to DataStudio for Scheduling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1397196061/p179031.png)

5.  Schedule the data analytics node.

    1.  Create a node of the Hologres development type in DataWorks.

        After you click **Go to DataStudio for Scheduling** in HoloStudio, the **Create Node** dialog box of the DataStudio page automatically appears. In the **Create Node** dialog box, set **Node Type**, **Node Name**, and **Location**. Set the **Node Type** parameter to **Hologres Development**.

        If you want to rerun an existing job, you do not need to create a data analytics node.

    2.  On the editing page of the data analytics node, click **Update Node** to synchronize the partitioned table information to the node.

        ![Synchronize data](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2397196061/p179073.png)

6.  Set the scheduling parameters of the data analytics node.

    On the editing page of the data analytics node, click **Properties** in the right-side navigation pane to set the scheduling parameters of the node. For more information, see [Scheduling parameters]().

    Perform the following steps:

    1.  Set the parameters in the General section.

        In the **General** section, specify a custom value for the $\{bizdate\} variable in the **Arguments** field.

        ![General section](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2397196061/p179120.png)

    2.  Set the parameters in the Schedule section.

        In the **Schedule** section, set **Execution Mode** to **Normal**. Set other parameters as needed. For more information, see [Time properties]().

        ![Schedule section](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2397196061/p179125.png)

    3.  Set the parameters in the Dependencies section.

        In the **Dependencies** section, perform the following steps to configure a root node:

        1.  Set **Auto Parse** to **Yes**.
        2.  Click **Use Root Node** in the Parent Nodes section.
        3.  After DataStudio parses and displays the root node as a parent node, change the value of **Auto Parse** to **No**. For more information, see [Dependencies]().
        ![Dependencies section](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2397196061/p179148.png)

        You can also select an existing parent node based on your workflow logic.

7.  Save and deploy the node for scheduling.

    1.  After you set the scheduling parameters of the data analytics node, click the ![Save icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2397196061/p179177.png) icon in the top navigation bar.

    2.  In the top navigation bar, click the ![ ](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2397196061/p179178.png) icon.

    3.  In the upper-right corner, click the **Deploy** icon to go to the Create Deploy Task page.

    4.  On the Create Deploy Task page, find the node and click **Deploy** in the **Actions** column.

    5.  In the **Create Deploy Task** dialog box, click **Deploy**.

    6.  After the node is deployed, click **Operation Center** in the top navigation bar of the **Create Deploy Task** page to generate retroactive data for the node.

    7.  In the left-side navigation pane, choose **Cycle Task Maintenance** \> **Cycle Task**. On the page that appears, select the task that you want to deploy.

    8.  In Operation Center, right-click the deployed node and choose **Run** \> **Current Node Retroactively**.

    9.  In the **Current Node Retroactively** dialog box, set the required parameters.

8.  After the retroactive data generation task is run, go back to HoloStudio. In the top navigation bar, click the ![ ](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2397196061/p179202.png) icon to reload the page. In the **PG management** module of the left-side navigation pane, you can find the scheduled partitioned table and view the data in the table.

    Double-click the partitioned table that is scheduled and click **Data Preview** to view the data in the table.

    You can also execute the following SQL statements to view the data of the parent table or partition table.

    ```
    SELECT * FROM  bank_data_holo;
    ```


