# Create and manage foreign tables

Foreign tables are tables whose data is not stored in Hologres. The PG management module of HoloStudio allows you to create, view, and delete foreign tables in a visualized manner. This topic describes how to use the PG management module of HoloStudio to manage foreign tables.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased and bound to a DataWorks workspace. For more information, see [Quick start to HoloStudio](/intl.en-US/Common Development Tools/HoloStudio/Quick start to HoloStudio.md).

Hologres is seamlessly integrated with the big data ecosystem. This allows you to query the data of the foreign tables and import the data to Hologres. Only MaxCompute tables are supported.

## Limits

-   Hologres can read data from MaxCompute tables across workspaces. However, you must make sure that your account can access MaxCompute tables in another workspace. Otherwise, you cannot create foreign tables in HoloStudio to directly read data from MaxCompute tables.
-   Make sure that a MaxCompute table exists before you create a foreign table in HoloStudio.
-   The fields of the foreign table in Hologres are consistent with those of the MaxCompute table. You can search for a MaxCompute table. Then, HoloStudio generates a foreign table that has the same fields as the MaxCompute table.

## Create a foreign table

1.  Go to the [Hologres console](https://hologram.console.aliyun.com/#/instance). In the top navigation bar of the console, move the pointer over the ![Show icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4259623061/p134229.png) icon and select the region where your Hologres instance resides.

2.  In the left-side navigation pane, click **Instances**. On the **Hologres Instances** page, click **Go to DataWorks - HoloStudio**.

3.  In the top navigation bar, click the ![Show icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5770096061/p171669.png) icon and select the workspace.

4.  In the left-side navigation pane, click the ![PG management](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5770096061/p171904.png) icon to go to the PG management page.

5.  Move the pointer over the ![Create icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5770096061/p171689.png) icon and click **External Table** to create an external table.

6.  On the page that appears, set the required parameters.

    ![Configure the external table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6770096061/p172071.png)

    |Section/Tab|Parameter|Description|
    |-----------|---------|-----------|
    |General|Interactive Analytics Database|The database where the table resides.|
    |Table name|The name of the foreign table.|
    |External Service|Types|The service type of the foreign table. Only MaxCompute tables are supported.|
    |Table|The source MaxCompute table to be mapped.|
    |Field Synchronization|The fields to be synchronized.|
    |Partition Synchronization|The partitions to be synchronized.|

7.  Click **Commit**.


## Query data of the foreign table

In the left-side navigation pane, click **PG management** and double-click the foreign table that you want to view.

![Foreign table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6770096061/p172085.png)

On the page that appears, you can preview the data and view the SQL statements used to create the table.

-   In the upper-right corner, click **Generate DDL Statement** to view the SQL statements used to create the table.

    ![DDL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6770096061/p172086.png)

-   Click **Data Preview** to preview the data of the foreign table.

    ![Preview the data of the foreign table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6770096061/p172087.png)


## Delete a foreign table

In the left-side navigation pane, right-click the table that you want to delete and click **Delete Table**.

![Delete a foreign table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6770096061/p172088.png)

