---
keyword: [HoloStudio, create internal tables, view tables, PG management]
---

# Create and manage internal tables

The PG management module of HoloStudio allows you to create, view, and delete internal tables in a visualized manner. This topic describes how to use the PG management module of HoloStudio to manage internal tables.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased and bound to a DataWorks workspace. For more information, see [Quick start to HoloStudio](/intl.en-US/Common Development Tools/HoloStudio/Quick start to HoloStudio.md).

## Create an internal table

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance). In the top navigation bar, move the pointer over the ![Show icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4259623061/p134229.png) icon and select the region where your Hologres instance resides.

2.  In the left-side navigation pane, choose **Instances** \> **Hologres Instances**. On the Hologres Instances page, click **Go to DatatWorks - HoloStudio**.

3.  In the top navigation bar, click the ![Select a workspace](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5770096061/p171669.png) icon and select the destination workspace.

4.  In the left-side navigation pane, click the ![PG management](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5770096061/p171904.png) icon to go to the PG management page.

5.  Move the pointer over the ![Create icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5770096061/p171689.png) icon and click **Table** to create a table.

    You can also click **Create Table** on the homepage of HoloStudio.

    ![Create an internal table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1166196061/p171911.png)

6.  On the page that appears, set the required parameters.

    ![Configure the table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1166196061/p171912.png)

    |Section/Tab|Parameter|Description|
    |-----------|---------|-----------|
    |**General**|**Interactive Analytics Database**|The database where the table resides.|
    |**Table Name**|The name of the table.|
    |**Description**|The description of the table.|
    |**Field**|**Create Field**|The button that is used to add a field to the table.|
    |**Field Name**|The name of the field to be added to the table.|
    |**Data Type**|The data type of the field.|
    |**Primary Key Field**|Specifies whether to use the field as the primary key for the table.|
    |**Optional**|Specifies whether the field can be left empty.|
    |**Array**|Specifies whether the field is an ordered array of elements.|
    |**Description**|The description of the field.|
    |**Actions**|The operations that you can perform on the field. You can click **Delete** to delete the field and click **Move Up** or **Move down** to move the field up or down.|
    |**Properties**|**Storage Mode**|The storage mode of the table. Valid values: **Column Store** and **Row Store**.Default value: **Column Store**. |
    |**Lifecycle**|The lifecycle of a record. If a record is not updated within that period, the system deletes the record.Default value: **Permanent** |
    |**Clustering Index**|The index that is used to sort columns.The type of index determines the order of fields. Clustering indexes accelerate range and filter queries of indexed columns. |
    |**Dictionary Code Columns**|Hologres allows you to create a dictionary for the values of specified columns.Dictionary encoding can convert string comparisons to numeric comparisons to accelerate queries such as GROUP BY and FILTER.

By default, all **text** columns are stored in the dictionary encoding column. |
    |**Bitmap Column**|Hologres allows you to build bit codes in bit columns.You can filter the data that meets the query conditions based on the specified fields by using Hologres.

By default, all **text** columns are set to bitmap columns. |
    |**Partitioned Table**|**PARTITION BY LIST**|The partition field.|

7.  Click **Commit**.

    You can develop data in the table.


## View an internal table

In the left-side navigation pane, click **PG management** and double-click the table that you want to view. On the page that appears, you can preview the data and view the SQL statements used to create the table.

-   In the upper-right corner, click **Generate DDL Statement** to view the SQL statements used to create the table.
-   Click **Data Preview** to view the table data.

## Delete an internal table

In the left-side navigation pane, right-click the table that you want to delete and click **Delete Table**.

![Delete an internal table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1166196061/p171935.png)

