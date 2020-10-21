---
keyword: [import data from a client, HoloStudio]
---

# Upload local files

This topic describes how to use HoloStudio to import data from a client to Hologres.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased and bound to a DataWorks workspace. For more information, see [Quick start to HoloStudio](/intl.en-US/HoloStudio/Quick start to HoloStudio.md).

The Data Analytics module of HoloStudio provides the **Upload files locally with one click** submodule. In this submodule, you can import data from a client in a visualized manner.

You can also execute the `COPY` statement to import data from a client. For more information, see [Use the COPY statement to import data from a client to Hologres](/intl.en-US/Data Access/Client data/Use the COPY statement to import data from a client to Hologres.md).

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar of the console, move the pointer over the ![Triangle](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4259623061/p134229.png) icon and select the region where your Hologres instance resides.

3.  In the left-side navigation pane, click **Instances**. On the **Hologres Instances** page, click **Go to DataWorks - HoloStudio**.

4.  Create a table.

    In the **SQL console**, execute SQL statements to create a table to which data in the local file is imported. For more information, see [SQL Console](/intl.en-US/HoloStudio/SQL Console.md). In this example, the following SQL statements are executed to create a table:

    ```
    BEGIN;
    CREATE TABLE if not EXISTS holo_bank (
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
     y int8
    );
    COMMIT;
    ```

    Alternatively, you can create a table on the **PG management** page. For more information, see [Create a table](/intl.en-US/HoloStudio/PG Management/Manage tables.md).

5.  Create a node for uploading the local file.

    1.  On the **HoloStudio** page, click the ![Data Analytics](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4259623061/p134237.png) icon in the left-side navigation pane.

    2.  On the **Data Analytics** page, move the pointer over the ![Create](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4259623061/p134243.png) icon.

    3.  Click **Upload files locally with one click**.

    4.  In the **One-click local file Upload** dialog box, set the parameters as required.

        ![Upload](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7160723061/p134497.png)

        |Parameter|Description|
        |---------|-----------|
        |Target Library|The name of the Hologres database where the table to which data in the local file is imported resides.|
        |Target Schema|The name of the schema where the table to which data in the local file is imported resides.If you have created no schema, select the default **public** schema. If you have created a schema, you can select the created schema. |
        |Select the data table to import|The name of the table to which data in the local file is imported.|

    5.  Click **Next Step**.

    6.  In the **One-click local file Upload** dialog box, set the parameters as required.

        ![Select file](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7160723061/p134509.png)

        |Parameter|Description|
        |---------|-----------|
        |Select File|The local file to upload.You can upload only a file in the .txt, .csv, or .log format. |
        |Select separator|        -   The delimiter of fields in the file. You can select one of the following delimiters: **Comma**
        -   **Tab**
        -   **Semicolon**
        -   **Space**
        -   **\|**
        -   **\#**
        -   **&**
You can also use a custom delimiter. |
        |Original character set|        -   **GBK**
        -   **UTF-8**
        -   **CP936**
        -   **ISO-8859** |
        |First behavior title|Specifies whether to use the first row as the table header.|

    7.  Click **Commit**.

6.  In the **SQL console** or on the **PG management** page, view the uploaded data. For more information, see [SQL Console](/intl.en-US/HoloStudio/SQL Console.md) or [Check a table](/intl.en-US/HoloStudio/PG Management/Manage tables.md).


