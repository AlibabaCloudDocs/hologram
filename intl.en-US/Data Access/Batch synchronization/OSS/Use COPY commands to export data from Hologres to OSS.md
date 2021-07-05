---
keyword: [COPY commands, Hologres, export data to OSS, hg\_dump\_to\_oss]
---

# Use COPY commands to export data from Hologres to OSS

Alibaba Cloud Object Storage Service \(OSS\) is a secure, cost-effective, and reliable cloud storage service. This topic describes how to run the `copy to` and `hg_dump_to_oss` commands to export query results from Hologres to a specified OSS bucket.

## Limits

-   Hologres instances allow only users who are granted the `pg_execute_server_program` permission and superusers to run the `hg_dump_to_oss` command to export data from Hologres to OSS. Superusers can execute one of the following statements to grant the `pg_execute_server_program` permission to users:

    ```
    -- If the current database uses the simple permission model, execute the following statement:
    call spm_grant('pg_execute_server_program','Alibaba Cloud account ID, Alibaba Mail address, or RAM user');
    
    -- If the current database uses the standard PostgreSQL authorization model, execute the following statement:
    grant pg_execute_server_program to Alibaba Cloud account ID, Alibaba Mail address, or RAM user;
    ```

-   The amount of data that you can export from Hologres to OSS at a time is up to 5 GB.

## Command description

-   [COPY commands](#section_k09_6g7_6q9): `COPY` commands are used to transmit data between PostgreSQL tables and standard file systems. Hologres supports `COPY` commands. In Hologres, you can run the `COPY TO` command to copy the query results of a SELECT statement to an output medium, such as a file. You can run the `COPY FROM` command to copy data from a file to a table.
-   [hg\_dump\_to\_oss command](#section_i1y_s65_h1e): You can run this command to dump Hologres query results to a specified OSS bucket.

## COPY commands

This section describes the syntax and parameters of the `COPY TO` command.

-   Syntax

    ```
    COPY ( query ) TO { PROGRAM 'command' | STDOUT }
        [ [ WITH ] ( option [, ...] ) ]
    ```

    The option in the syntax can be one of the following parameters:

    ```
        FORMAT [format_name]
        DELIMITER 'delimiter_character'
        NULL 'null_string'
        HEADER
        QUOTE 'quote_character'
        ESCAPE 'escape_character'
        ENCODING 'encoding_name'
    ```

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |query|The query statement. Do not delete the parentheses before and after the query statement.|
    |PROGRAM|A command that you want to run. The data to be exported is the standard input of the command.|
    |STDOUT|The output of the command. The output is synchronized to the application on the client.|
    |FORMAT|The format of the data after export. Valid values: TEXT, CSV, and BINARY. Default value: TEXT.|
    |DELIMITER|The delimiter that you want to use to separate fields in each row in the file to be generated. The specified delimiter must be a single-byte character. Specify this parameter based on data formats:    -   If the data format is TEXT, the default delimiter is a tab character \(\\t\).
    -   If the data format is CSV, the default delimiter is a comma \(,\).
    -   If the data format is BINARY, do not specify this parameter. |
    |NULL|The string that you want to use to represent an empty value. Specify this parameter based on data formats:    -   If the data format is TEXT, the default string is \\N.
    -   If the data format is CSV, the default string is an unquoted empty string.
    -   If the data format is BINARY, do not specify this parameter. |
    |HEADER|Specifies whether the file to be generated contains a header row. Values in the row are fields in the Hologres query results that you want to export. Specify this parameter only when the data format is CSV.|
    |QUOTE|The character that appears before a character that is the same as the value of the quote parameter. The specified character must be a single-byte character. The default character is the same as the value of the quote parameter. Specify this parameter only when the data format is CSV.|
    |ENCODING|The encoding method that you want to use for the file to be generated. If you do not specify this parameter, the file is generated based on the encoding method that is used by the current client.|


## hg\_dump\_to\_oss command

This section describes the syntax and parameters of the `hg_dump_to_oss` command. In Hologres, the `hg_dump_to_oss` command is used together with the `COPY TO` command.

-   Syntax

    ```
    COPY (query) TO PROGRAM 'hg_dump_to_oss --AccessKeyId <accessid> --AccessKeySecret <accesskey> --Endpoint <ossendpoint> --BucketName <bucketname> --<DirName> <dirname> --BatchSize <xxx> ' (DELIMITER ',', HEADER true, FORMAT CSV);
    ```

-   Parameters

    |Parameter|Description|Example|
    |---------|-----------|-------|
    |query|The query statement.|`select * from dual;`|
    |AccessKeyId|The AccessKey ID of your Alibaba Cloud account. You can obtain the AccessKey ID from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.

|N/A|
    |AccessKeySecret|The AccessKey secret of your Alibaba Cloud account. You can obtain the AccessKey secret from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.

|N/A|
    |Endpoint|The classic network endpoint of the OSS bucket. You can view the classic network endpoint on the details page of the bucket. To go to the details page of the bucket, log on to the OSS console and click the bucket name on the [Buckets](https://oss.console.aliyun.com/bucket) page.

|oss-cn-beijing-internal.aliyuncs.com|
    |BucketName|The name of the OSS bucket.|dummy\_bucket|
    |DirName|The directory to which data is exported in the OSS bucket.|testdemo/|
    |BatchSize|The number of rows that are processed by the `hg_dump_to_oss` command at a time. Default value: 1000.|5000|
    |DELIMITER|The delimiter that you want to use to separate fields in the query results. The default delimiter is a tab character \(\\t\).|','|


## Examples

The following examples show you how to run the `hg_dump_to_oss` and `COPY TO` commands in Hologres.

```
-- Dump data from a Hologres internal table to a specified OSS bucket.
COPY (select * from holo_test LIMIT 2) TO PROGRAM 'hg_dump_to_oss --AccessKeyId <access id> --AccessKeySecret <access key> --Endpoint oss-cn-hangzhou-internal.aliyuncs.com --BucketName hologres-demo --DirName holotest/ --BatchSize 3000' DELIMITER ',';

-- Dump data from a Hologres foreign table to a specified OSS bucket.
COPY (select * from foreign_holo_test LIMIT 20) TO PROGRAM 'hg_dump_to_oss --AccessKeyId <access id> --AccessKeySecret <access key> --Endpoint oss-cn-hangzhou-internal.aliyuncs.com --BucketName hologres-demo --DirName holotest/ --BatchSize 3000' (DELIMITER ',', HEADER true);

-- Dump data from a Hologres table to a specified OSS bucket in another region.
COPY (select * from holo_test_1 LIMIT 20) TO PROGRAM 'hg_dump_to_oss --AccessKeyId <access id> --AccessKeySecret <access key> --Endpoint oss-cn-beijing-internal.aliyuncs.com --BucketName hologres-demo --DirName holotest/ --BatchSize 3000' (DELIMITER ',', HEADER true, FORMAT CSV);
```

**Note:** Hologres allows you to export data to OSS buckets across regions. For example, you can export data that is queried from a Hologres instance in the China \(Hangzhou\) region to an OSS bucket in the China \(Beijing\) region.

## Common errors

The following items describe common errors and solutions:

-   `ERROR: syntax error at or near ")"LINE 1: COPY (select 1,2,3 from ) TO PROGRAM 'hg_dump_to_oss2 --Acce...`

    This error message is returned because the query statement that is specified in the query parameter is invalid. Check and correct the query statement.

-   `DETAIL: child process exited with exit code 255`

    This error message is returned because the network type that is specified for the OSS bucket is invalid. To access the OSS bucket in the Alibaba Cloud public cloud, use the **classic network** endpoint of the OSS bucket.

-   `DETAIL: command not found`

    This error message is returned because the **PROGRAM** parameter in the `DUMP TO OSS` command is not set to hg\_dump\_to\_oss.

-   `ERROR: program "hg_dump_to_oss ..." failed DETAIL: child process exited with exit code 101`

    This error message is returned because the specified AccessKeyId parameter is invalid. Use the AccessKey ID of your Alibaba Cloud account.

-   `ERROR: program "hg_dump_to_oss ..." failed DETAIL: child process exited with exit code 102`

    This error message is returned because the specified AccessKeySecret parameter is invalid. Use the AccessKey secret of your Alibaba Cloud account.

-   `ERROR: program "hg_dump_to_oss ..." failed DETAIL: child process exited with exit code 103`

    This error message is returned because the specified Endpoint parameter is invalid. Use the correct classic network endpoint of the OSS bucket.

-   `ERROR: program "hg_dump_to_oss ..." failed DETAIL: child process exited with exit code 104`

    This error message is returned because the specified BucketName parameter is invalid. Use the correct bucket name.

-   `ERROR: program "hg_dump_to_oss ..." failed DETAIL: child process exited with exit code 105`

    This error message is returned because a required parameter is missing. Check whether all required parameters are correctly set.

-   `ERROR: program "hg_dump_to_oss ..." failed DETAIL: child process exited with exit code 255`

    This error message is returned because the server where your Hologres instance resides cannot connect to the network where the specified OSS bucket resides. Change the specified endpoint of the OSS bucket. For example, you can use the classic network endpoint of the OSS bucket. For more information about the endpoints of OSS buckets, see [Regions and endpoints](/intl.en-US/Developer Guide/Endpoint/Regions and endpoints.md).


