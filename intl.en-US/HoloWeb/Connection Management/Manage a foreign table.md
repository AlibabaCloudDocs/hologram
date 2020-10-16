---
keyword: [HoloWeb, foreign table, preview the table data, preview the DDL statement]
---

# Manage a foreign table

This topic describes how to use HoloWeb to create, edit, and delete a foreign table that is sourced from MaxCompute. This topic also describes how to preview the source data and the Data Definition Language \(DDL\) statement of the foreign table.

-   An Alibaba Cloud account is created..
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).
-   The purchased Hologres instance is connected to HoloWeb. For more information, see [Manage a connection](/intl.en-US/HoloWeb/Connection Management/Manage a connection.md).

## Create a foreign table

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8969182061/p141749.png)

3.  In the left-side navigation pane, click Instances. On the **Hologres Instances** page, click **Log in to the hologres database**.

4.  On the **Connection Management** page of the HoloWeb console, click the **External table** tab. The New external table tab appears.

    You can also go to the New external table tab by performing the following steps:

    1.  In the left-side navigation pane of the **Connection Management** page, click **My connection**. All the configured connections are displayed.

    2.  Click the relevant connection and then click **Database**. All the databases that are created in the connected Hologres instance are displayed.

    3.  Click the relevant database and then click **Schema**. All the available schemas are displayed.

    4.  Right-click the schema under which you want to create a foreign table and select **New external table**.

5.  On the **New external table** tab, set the parameters as required. Then, click **Submit form**.

    ![New external table](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6566762061/p132279.png)

    |Parameter|Description|
    |---------|-----------|
    |Connection name|The name of the connection based on which you want to create the foreign table.|
    |Database|The name of the database where the foreign table is to be created.|
    |Table name|The name of the foreign table.|
    |Description|The description of the foreign table.|
    |Mode|The schema under which the foreign table is to be created.Default value: **public**. You can also select a custom schema. |
    |Type|The service type of the source table.The default value is MaxCompute, which cannot be changed. |
    |Server List|The server on which the source table is stored. You can select the **odps\_server** server that is created at the underlying layer of Hologres. For more information, see [Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k).|
    |Table|The source table in MaxCompute to be mapped.The value of this parameter is in the format of **project.table\_name**.

**Note:**

    -   You cannot map a MaxCompute table that is stored in a region different from that of the selected Hologres instance.
    -   After you specify a MaxCompute table, the fields in the table are displayed on the Field tab. You can map all the fields or select some of the fields as required. |


## Edit a foreign table

1.  In the left-side navigation pane of the Connection Management page, click **My connection**. All the configured connections are displayed.

2.  Click the relevant connection and then click **Database**. All the databases that are created in the connected Hologres instance are displayed.

3.  Click the relevant database and then click **Schema**. All the available schemas are displayed.

4.  Click the schema for which you want to edit a foreign table and then click **Foreign table**. All the foreign tables that are created in the database under the schema are displayed.

5.  Right-click the foreign table that you want to edit and select **Edit table**.

6.  You can modify the fields and partition of the foreign table that is mapped from the source MaxCompute table based on your business requirements.

    ![Edit table](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7566762061/p132300.png)

7.  Click **Submit form**.


## Delete a foreign table

1.  In the left-side navigation pane of the Connection Management page, click **My connection**. All the configured connections are displayed. Then, find the foreign table that you want to delete.

    For more information about how to find the desired foreign table, see Step 1 to Step 4 in [Edit a foreign table](#section_ppd_18h_f0e).

2.  Right-click the foreign table and select **Delete table**.


## Preview the table data

1.  In the left-side navigation pane of the Connection Management page, click **My connection**. All the configured connections are displayed. Then, find the foreign table of which you want to preview the source data.

    For more information about how to find the desired foreign table, see Step 1 to Step 4 in [Edit a foreign table](#section_ppd_18h_f0e).

2.  Double-click the table.

    On the tab that appears, click Data preview and preview the source data, as shown in the following figure.

    ![Data preview](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7566762061/p132309.png)


## Preview the DDL statement

1.  In the left-side navigation pane of the Connection Management page, click **My connection**. All the configured connections are displayed. Then, find the foreign table of which you want to preview the DDL statement.

    For more information about how to find the desired foreign table, see Step 1 to Step 4 in [Edit a foreign table](#section_ppd_18h_f0e).

2.  Double-click the table.

    On the tab that appears, click DDL statement and preview the DDL statement of the table, as shown in the following figure.

    ![DDL](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7566762061/p132312.png)


