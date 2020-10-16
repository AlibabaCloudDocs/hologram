---
keyword: [import a local file, HoloWeb]
---

# Import a local file to Hologres

This topic describes how to use HoloWeb to import a local file to Hologres.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).
-   The purchased Hologres instance is connected to HoloWeb. For more information, see [Manage a connection](/intl.en-US/HoloWeb/Connection Management/Manage a connection.md).

You can import only files smaller than 100 MB in size to Hologres in visualized mode by using HoloWeb. To import files that are larger than 100 MB in size, you must use the `COPY` statement. For more information, see [Use the COPY statement to import data from a client to Hologres](/intl.en-US/Data Access/Client data/Use the COPY statement to import data from a client to Hologres.md).

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8969182061/p141749.png)

3.  In the left-side navigation pane, click Instances. On the **Hologres Instances** page, click **Log in to the hologres database**.

4.  In the top navigation bar, click **Tools**.

5.  On the Tools page, choose **Data scheme** \> **Import** in the left-side navigation pane.

6.  On the Import tab, click **New data import**.

7.  In the **One-click local file Upload** dialog box, set the parameters as required in the **Select Target table** step.

    ![One-click local file Upload](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0680382061/p141331.png)

    |Parameter|Description|
    |---------|-----------|
    |Job Name|The name of the import task.|
    |Connection name|The name of the connection based on which you want to import the local file.|
    |Target Library|The name of the database to which the destination table belongs.|
    |Target Schema|The name of the schema to which the destination table belongs.If you have not created a custom schema, select the default **public** schema. If you have created a custom schema, you can select the created schema. |
    |Select the data table to import|The name of the destination table to which you want to import the local file.Before you import a local file, you must create a table in the destination database to store the local file. |

8.  Click **Next Step**.

9.  Set the parameters as required in the **Select Data Source table** step.

    ![Data source table](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0680382061/p141340.png)

    |Parameter|Description|
    |---------|-----------|
    |Select File|The local file to be uploaded.Only .txt, .csv, and .log files are supported. |
    |Select separator|The delimiter that is used to delimit the fields in the file. Valid values:    -   **Comma**
    -   **Tab**
    -   **Semicolon**
    -   **Space**
    -   **\|**
    -   **\#**
    -   **&**
You can also specify a custom delimiter. |
    |Original character set|The character set of the file. Valid values:    -   **GBK**
    -   **UTF-8**
    -   **CP936**
    -   **ISO-8859** |
    |First behavior title|Specifies whether to set the first row in the file as the title.|

10. Click **Next Step**.

11. In the **Import overview** step, click **Execution**.

    After you run the import task, you can view the schema, database, and table that are related to the task in the Import overview step. You can also view the status and start time of the task in this step.

    ![Import overview](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0680382061/p141354.png)

