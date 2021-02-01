---
keyword: [import an on-premises file, HoloWeb]
---

# Import an on-premises file to Hologres

This topic describes how to use HoloWeb to import an on-premises file to Hologres.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

You can import only files smaller than 100 MB in size to Hologres in a visualized manner by using HoloWeb. To import files that are larger than 100 MB in size, you must execute the `COPY` statement. For more information, see [Use the COPY statement to import data from a client to Hologres](/intl.en-US/Data Access/Client data/Use the COPY statement to import data from a client to Hologres.md).

1.  Connect the purchased Hologres instance to HoloWeb. For more information, see [Manage a connection](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a connection.md).

2.  On the HoloWeb page, click **System Management** in the top navigation bar.

3.  In the left-side navigation pane, choose **Data Synchronization** \> **Import On-premlses File**

4.  Click **New data import**.

5.  In the **One-click local file Upload** dialog box, set the parameters as required in the **Select Target table** step.

    ![One-click local file Upload](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0680382061/p141331.png)

    |Parameter|Description|
    |---------|-----------|
    |Job Name|The name of the import task.|
    |Connection name|The name of the required connection.|
    |Target Library|The name of the Hologres database.|
    |Target Schema|The name of the created schema in Hologres.If you have not created a schema, select the default schema **public**. If you have created a schema, you can select the created schema. |
    |Select the data table to import|The name of the destination table to which you want to import the data of on-premises file.Before you import an on-premises file, you must create a table in the destination database to store the data of the on-premises file. |

6.  Click **Next Step**.

7.  Set the parameters as required in the **Select Data Source table** step.

    ![Data source table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0680382061/p141340.png)

    |Parameter|Description|
    |---------|-----------|
    |Select File|The on-premises file to be uploaded.Only .txt, .csv, and .log files are supported. |
    |Select separator|Valid values:    -   **Comma**
    -   **Tab**
    -   **SEMICOLON**
    -   **Space**
    -   **\|**
    -   **\#**
    -   **&**
You can also specify a custom delimiter. |
    |Original character set|Valid values:    -   **GBK**
    -   **UTF-8**
    -   **CP936**
    -   **ISO-8859** |
    |First behavior title|Specifies whether to set the first row in the file as the title.|

8.  Click **Next Step**.

9.  In the **Import overview** step, click **Execution**.

    After you run the import task, you can view the schema, database, and table that are related to the task in the Import overview step. You can also view the status and start time of the task in this step.

    ![Import](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0680382061/p141354.png)


