---
keyword: [Hologres, DLF]
---

# Use DLF to read OSS data

This topic describes how to use Data Lake Formation \(DLF\) to read Object Storage Service \(OSS\) data in Hologres.

-   Hologres is activated. The version of your Hologres instance is V0.10 or later. A development tool is connected to your Hologres instance. In this example, HoloWeb is used. For more information, see [HoloWeb quick start](/intl.en-US/Quick Start/HoloWeb quick start.md).
-   OSS is activated. Data is uploaded to an OSS bucket. For more information, see [Get started with OSS](/intl.en-US/Quick Start/Get started with OSS.md).
-   DLF is activated. For more information, see [Quick start]().

Alibaba Cloud DLF is a fully managed service that helps you build data lakes in the cloud. The service provides unified permission management, unified metadata management, and automatic metadata extraction capabilities for data lakes in the cloud. For more information about DLF, see [Product introduction]().

DLF uses Alibaba Cloud OSS as the data store of data lakes in the cloud. In Hologres V0.10 and later, Hologres allows you to use DLF to read OSS data by creating foreign tables in a convenient and fast way. This simplifies data development and reduces O&M costs.

When you use DLF to read OSS data by creating a foreign table in Hologres, the foreign table is used to map fields in the source table, but is not used to store data. Data is still stored in OSS. For more information about the principles, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html).

## Limits

When you use DLF to read OSS data by creating a foreign table in Hologres, take note of the following limits:

-   This feature is supported only in Hologres V0.10 and later. You can view the version of your Hologres instance on the instance details page in the Hologres console. If the version of your Hologres instance is earlier than V0.10, [submit a ticket](https://workorder-intl.console.aliyun.com/) to upgrade your instance.
-   Before you use this feature, you must execute the following statement to install an extension as the superuser. An extension is installed at the database level. For each database, you need to install an extension only once. If you create a database, you must execute the statement again.

    ```
    create extension dlf_fdw;
    ```

-   You can use this feature to read data in the same region, but not across regions.
-   Hologres allows you to read data from OSS objects in the text or CSV, SequenceFile, Parquet, or ORC format.
-   When you create a server, you must use the classic network endpoints of DLF and OSS.

## Use DLF to read OSS data

Before you start, you must prepare a metadata table in DLF and ensure that the metadata is extracted in the table. For more information, see [Metadata management](). To use DLF to read OSS data by creating a foreign table in Hologres, perform the following steps:

1.  Install an extension.

    Before you use this feature, execute the following statement to install an extension in a database as the superuser. An extension is installed at the database level. For each database, you need to install an extension only once.

    ```
    create extension if not exists dlf_fdw;
    ```

2.  Create a server.

    After the extension is installed, you must create a server for Hologres to connect to DLF and OSS. You must use the classic network endpoints.

    -   Sample code

        ```
        create server if not exists <servername> foreign data wrapper dlf_fdw options
        (
            dlf_region 'cn-<region>',
            dlf_endpoint '<The classic network endpoint of DLF>',
            oss_endpoint '<The classic network endpoint of the OSS bucket>'
        );
        ```

    -   Parameters

        |Parameter|Description|Example|
        |---------|-----------|-------|
        |server\_name|The name of the server.|oss\_server|
        |endpoint|        -   The classic network endpoint of DLF. The endpoint is in the following format: `dlf-share.cn-<region>.aliyuncs.com`.
        -   The classic network endpoint of the OSS bucket. For more information about the endpoints of OSS buckets, see [OSS internal endpoints and VIP address ranges](/intl.en-US/Developer Guide/Endpoint/OSS internal endpoints and VIP address ranges.md).
|        -   DLF

            ```
dlf-share.cn-shanghai.aliyuncs.com
            ```

        -   OSS

            ```
oss-cn-shanghai-internal.aliyuncs.com
            ``` |

    -   Examples

        Create a server named dlf\_server in the China \(Shanghai\) region. Sample code:

        ```
        create server if not exists dlf_server foreign data wrapper dlf_fdw options
        (
            dlf_region 'cn-shanghai',
            dlf_endpoint 'dlf-share.cn-shanghai.aliyuncs.com', -- The classic network endpoint of DLF in the China (Shanghai) region.
            oss_endpoint 'oss-cn-shanghai-internal.aliyuncs.com' -- The classic network endpoint of the OSS bucket.
        );
        ```

3.  Optional. Create a user mapping.

    By default, the AccessKey ID and AccessKey secret of your Alibaba Cloud account are used to access DLF metadata and read table data. For more information about how to obtain the AccessKey ID and AccessKey secret of your Alibaba Cloud account, see the "Create an AccessKey pair" step of the [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md) topic. If you do not want to use your Alibaba Cloud account, you can use the following statement to create a user mapping and specify the account that you want to use to access data. Ensure that the specified account has the required permissions to query data. For more information, see [CREATE USER MAPPING](https://www.postgresql.org/docs/11/sql-createusermapping.html).

    ```
    create user mapping for <The UID of the account> server hm_dlf_server options
    (
        dlf_access_id 'accessid', dlf_access_key 'accesskey',
        oss_access_id 'accessid', oss_access_key 'accesskey'
    );
    ```

    Examples:

    ```
    -- Create a user mapping for your Alibaba Cloud account.
    create user mapping for current_user server hm_dlf_server options
    (
        dlf_access_id 'LTAI5txxx', dlf_access_key 'y8LUUyyy',
        oss_access_id 'LTAI5txxx', oss_access_key 'y8LUUyyy'
    );
    
    -- Create a user mapping for the RAM user whose UID is 123xxx.
    create user mapping for "p4_123xxx" server hm_dlf_server options
    (
        dlf_access_id 'LIlY5txxx', dlf_access_key 'KsjkXKyyy',
        oss_access_id 'LIlY5txxx', oss_access_key 'KsjkXKyyy'
    );
    ```

4.  Create one or more foreign tables.

    After the server is created, you can use the [IMPORT FOREIGN SCHEMA](/intl.en-US/Hologres SQL/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md) statement to create one or more foreign tables in Hologres to read OSS data that is extracted by DLF.

    -   Sample code

        ```
        import foreign schema remote_schema
            [ { limit to | except } ( table_name [, ...] ) ]
            from server server_name
            into local_schema 
            [ options ( option 'value' [, ... ] ) ]
        ```

    -   Parameters

        |Parameter|Description|
        |---------|-----------|
        |remote\_schema|The name of the metadatabase that is created in DLF.|
        |table\_name|The name of the metadata table that is created in DLF.|
        |server\_name|The name of the server that is created in Hologres.|
        |local\_schema|The name of the schema in Hologres.|
        |options|The options that you can set when you use the IMPORT FOREIGN SCHEMA statement. For more information, see [IMPORT FOREIGN SCHEMA](/intl.en-US/Hologres SQL/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md).|

    -   Examples

        Create a foreign table to map the metadata table named dlf\_oss\_test in the DLF metadatabase named dlfpro. The foreign table resides in the public schema of Hologres. Set the if\_table\_exist option to update to check whether a foreign table with the same name exists. If so, update the existing foreign table.

        ```
        import foreign schema dlfpro limit to
        (dlf_oss_test) 
        from server dlf_server  into public options(if_table_exist 'update');
        ```

        Map all tables in the DLF metadatabase named dlfpro to the public schema of Hologres to create multiple foreign tables in Hologres. These foreign tables are named after the tables in the dlfpro metadatabase in DLF.

        ```
        import foreign schema dlfpro 
        from server dlf_server  into public options(if_table_exist 'update');
        ```

5.  Query data.

    After one or more foreign tables are created, you can query data in these tables to read OSS data.

    ```
    select * from dlf_oss_test;
    ```


## Mappings between data types

|DLF data type|Hologres data type|
|-------------|------------------|
|INT|INT|
|BIGINT|BIGINT|
|STRING|TEXT|
|BOOLEAN|BOOLEAN|
|DATE|DATE|
|TIMESTAMP|TIMESTAMP|
|FLOAT|FLOAT|
|DOUBLE|DOUBLE|
|DECIMAL|DECIMAL|
|CHAR\(n\)|CHAR\(n\)|
|VARCHAR\(n\)|VARCHAR\(n\)|

