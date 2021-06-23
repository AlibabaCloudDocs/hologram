# Use foreign tables to access OSS data

This topic describes how to create a foreign table in Hologres to accelerate queries of Object Storage Service \(OSS\) data.

## Prerequisites

-   Hologres is activated. A development tool is connected to your Hologres instance. In this example, HoloWeb is used. For more information, see [HoloWeb quick start](/intl.en-US/Quick Start/HoloWeb quick start.md).
-   OSS is activated. Data is uploaded to an OSS bucket. For more information, see [Get started with OSS](/intl.en-US/Quick Start/Get started with OSS.md).
-   A bucket policy is configured to authorize you to access the data in the OSS bucket. If you do not have the required permissions, you cannot use the foreign table that you create to query the data in the OSS bucket. For more information about bucket policies in OSS, see [Bucket policy](/intl.en-US/SDK Reference/Java/Buckets/Bucket policy.md).

## Background information

OSS is a secure, cost-effective, and highly reliable cloud storage service that can store a large number of files of all types. Hologres is integrated with OSS. You can create foreign tables in Hologres to accelerate queries of OSS data, without the need to import and export data. Data is still stored in OSS. A foreign table in Hologres is used to map fields in the source table, but is not used to store data.

Hologres is compatible with PostgreSQL. The principles of accessing OSS data by creating foreign tables are the same as the principles of postgres\_fdw. For more information, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html).

## Limits

When you create a foreign table in Hologres to access OSS data, take note of the following limits:

-   This feature is supported only in Hologres V0.10 and later. You can view the version of your Hologres instance on the instance details page in the Hologres console. If the version of your Hologres instance is earlier than V0.10, [submit a ticket](https://workorder-intl.console.aliyun.com/) to upgrade your instance.
-   Before you create a foreign table in Hologres, you must execute the following statement to install an extension as the superuser. An extension is installed by the superuser of an instance at the database level. For each database, you need to install an extension only once.

    ```
    create extension oss_fdw;
    ```

-   Only specific data types are supported, including STRING, TEXT, BIGINT, INT, FLOAT, DOUBLE, BOOLEAN, and DATE.
-   When you query data in a table, the first N rows of the table cannot be skipped.
-   Hologres allows you to query OSS data by creating foreign tables. If you need to import OSS data to Hologres, we recommend that you use the data integration feature. For more information, see [Hologres Reader]().

## Create a foreign table to access OSS data

To access OSS data by creating a foreign table in Hologres, perform the following steps:

1.  Install an extension.

    Before you create a foreign table in Hologres, you must execute the following statement to install an extension in a database as the superuser. An extension is installed by the superuser of an instance at the database level. For each database, you need to install an extension only once.

    ```
    create extension oss_fdw;
    ```

2.  Create a server.

    After the extension is installed, you must create a server to connect to OSS.

    -   Sample code

        ```
        CREATE SERVER <server_name> FOREIGN DATA WRAPPER oss_fdw 
        OPTIONS (
          endpoint 'https://<The classic network endpoint of the OSS bucket>'
        );
        ```

    -   Parameters

        |Parameter|Description|Example|
        |---------|-----------|-------|
        |server\_name|The name of the server.|oss\_server|
        |endpoint|The classic network endpoint of the OSS bucket. The endpoint must start with `http://` or `https://`. For more information about the endpoints of OSS buckets, see [OSS internal endpoints and VIP address ranges](/intl.en-US/Developer Guide/Endpoint/OSS internal endpoints and VIP address ranges.md).|oss-cn-shanghai-internal.aliyuncs.com|

3.  Create a foreign table.

    After the server is created, you can create a foreign table in Hologres to query the data in the OSS bucket. You can create a foreign table in the Parquet or text format based on your business requirements. The created foreign table must be in the same file format as the source table in OSS. Otherwise, data may fail to be read from OSS.

    -   Sample code

        Use the following statement to create a foreign table in the Parquet format:

        ```
        create foreign table <tablename>(
          col type, 
          col type
        ) server <server_name>
         options (
          dir './<dir_url>/',
          format 'parquet', 
        )
        ```

        Use the following statement to create a foreign table in the text format:

        ```
        create foreign table <tablename>(
          col type, 
          col type
        ) server <server_name>
         options (
          dir './<dir_url>/',
          format 'text',
          delimiter ','
        )
        ```

    -   Parameters

        |Parameter|Description|Example|
        |---------|-----------|-------|
        |tablename|The name of the foreign table.|oss\_table|
        |col|The name of the field.|id|
        |type|The data type of the field.|int|
        |server\_name|The name of the server.|oss\_server|
        |dir\_url|The OSS path of the source table. Specify the folder where the table is stored. Take note of the following items:        -   Hologres allows you to specify the folder where an object is stored in OSS, instead of the complete OSS path of the object that contains the name of the object.
        -   Hologres reads all objects in the specified OSS path for parsing, such as the orc folder. Make sure that the objects of a folder are in the same file format. Otherwise, garbled characters may be read.
        -   Do not include the name of an object in the OSS path that you specify. For example, if you specify `./oss-test/orc/region_zlib_dict.orc` as the OSS path, the foreign table can be created but no data can be read.
|For example, a table in the ORC format is stored in the following OSS path:        ```
oss://oss-test/orc/region_zlib_dict.orc
        ```

Specify `./oss-test/orc/` as the OSS path of the table.|
        |format|The file format of the source table in OSS. Valid values:        -   parquet
        -   orc
        -   text
|parquet|
        |delimiter|The delimiter of fields in the source table in the text format. **Note:** This parameter is required when the format parameter is set to text.

|,|

4.  Query data in the foreign table.

    After the foreign table is created, you can query data in the foreign table. Sample code:

    ```
    SELECT COUNT (*) FROM osstest; -- Query the number of rows in the osstest table. 
    
    SELECT * FROM osstest limit 10; -- Query data in the osstest table and return 10 data entries. 
    ```


## Performance optimization

Hologres uses foreign tables to read OSS data. To be specific, Hologres reads all objects of a folder from OSS to create a foreign table. When you partition the foreign table, we recommend that you use each object in the folder in OSS as a partition. We also recommend that you control the size of each object to balance the size of each partition. This prevents a large source table from consuming more computing resources and affecting the overall performance.

