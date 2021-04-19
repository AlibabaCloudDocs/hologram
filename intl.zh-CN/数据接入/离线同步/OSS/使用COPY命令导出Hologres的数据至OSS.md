---
keyword: [COPY命令, Hologres, 导出数据至OSS, hg\_dump\_to\_oss]
---

# 使用COPY命令导出Hologres的数据至OSS

阿里云对象存储（Object Storage Service，简称 OSS）是阿里云提供的安全、低成本及高可靠的云存储服务。本文指导您使用`copy to`命令语句和`hg_dump_to_oss`命令语句将查询的数据导出到指定的OSS。

## 使用限制

-   仅当前Hologres实例的Superuser或拥有`pg_execute_server_program`权限的用户，才可以使用`hg_dump_to_oss`导出Hologres的数据至OSS。Superuser可以授予其他用户`pg_execute_server_program`权限，命令如下。

    ```
    --DB开启简单权限模型，执行以下语句
    call spm_grant('pg_execute_server_program','云账号ID/云邮箱/RAM账号');
    
    --DB使用的是专家权限模型，执行以下语句
    grant pg_execute_server_program to 云账号ID/云邮箱/RAM账号;
    ```

-   单次导入至OSS的数据量不能超过5GB。

## 命令介绍

-   [COPY命令](#section_k09_6g7_6q9)：`COPY`命令是PostgresSQL表和标准文件系统之间移动数据的工具，Hologres支持`COPY`命令。`COPY TO`语句将SELECT查询的结果内容复制到一个文件或者其他输出介质中。`COPY FROM`语句用于从一个文件复制数据到一个表。
-   [hg\_dump\_to\_oss命令](#section_i1y_s65_h1e)：用于将在Hologres中查询的结果dump到指定的OSS。

## COPY命令

如下内容将为您介绍`COPY TO`命令的具体语法格式和参数说明。

-   命令格式

    ```
    COPY ( query ) TO { PROGRAM 'command' | STDOUT }
        [ [ WITH ] ( option [, ...] ) ]
    ```

    其中，option可以是下列之一：

    ```
        FORMAT [format_name]
        DELIMITER 'delimiter_character'
        NULL 'null_string'
        HEADER
        QUOTE 'quote_character'
        ESCAPE 'escape_character'
        ENCODING 'encoding_name'
    ```

-   参数说明

    |参数|描述|
    |--|--|
    |query|输入的查询语句。查询语句前后的圆括号必需保留。|
    |PROGRAM|一个要执行的命令，输出会写入到该命令的标准输入。|
    |STDOUT|指定输出，该输出将同步到到客户端应用。|
    |FORMAT|选择数据格式，其格式可以为text，csv 或者binary。默认为text。|
    |DELIMITER|指定分隔文件每行中各列的字符，这必须是一个单一的单字节字符。各数据格式的字符说明如下：    -   text格式中默认是一个制表符（如\\t）。
    -   csv格式默认是一个逗号（,）。
    -   binary格式时不支持使用这个选项。 |
    |NULL|指定表示一个空值的字符串。各数据格式的字符说明如下：    -   text格式中默认是\\N。
    -   csv格式默认是一个未加引用的空串。
    -   binary格式时不支持使用这个选项。 |
    |HEADER|指定文件包含标题行，其中有每一列的名称。只有csv格式支持该选项。|
    |QUOTE|指定应该出现在一个匹配QUOTE值的数据字符之前的字符，这必须是一个单一的单字节字符。 默认和QUOTE值一样。只有csv格式支持该选项。|
    |ENCODING|指定文件按照encoding\_name编码。如果省略，将使用当前的客户端编码。|


## hg\_dump\_to\_oss命令

如下内容将为您介绍`hg_dump_to_oss`命令的具体语法格式和参数说明，在Hologres中`hg_dump_to_oss`命令与`COPY TO`命令组合在一起使用。

-   命令格式

    ```
    COPY (query) TO PROGRAM 'hg_dump_to_oss --AccessKeyId <accessid> --AccessKeySecret <accesskey> --Endpoint <ossendpoint> --BucketName <bucketname> --<DirName> <dirname> --BatchSize <xxx> ' (DELIMITER ',', HEADER true, FORMAT CSV);
    ```

-   参数说明

    |参数|描述|示例|
    |--|--|--|
    |query|输入的查询语句。|`select * from dual;`|
    |AccessKeyId|当前账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。

|无|
    |AccessKeySecret|当前账号的AccessKey SECRET。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。

|无|
    |Endpoint|OSS的经典网络访问域名。您可以单击[Bucket列表](https://oss.console.aliyun.com/bucket)页面的目标Bucket名称，进入Bucket详情页查看。

|oss-cn-beijing-internal.aliyuncs.com|
    |BucketName|OSS对应的Bucket名称。|dummy\_bucket|
    |DirName|OSS存放输出结果的目录。|testdemo/|
    |BatchSize|每次执行`hg_dump_to_oss`的行数，默认为1000。|5000|
    |DELIMITER|结果列之间的分隔符，默认为制表符（Tab-separated Values，简称TSV）。|','|


## 使用示例

在Hologres中`hg_dump_to_oss`命令与`COPY TO`命令的使用示例如下。

```
--将Hologres内部表数据dump到指定OSS
COPY (select * from holo_test LIMIT 2) TO PROGRAM 'hg_dump_to_oss --AccessKeyId <access id> --AccessKeySecret <access key> --Endpoint oss-cn-hangzhou-internal.aliyuncs.com --BucketName hologres-demo --DirName holotest/ --BatchSize 3000' DELIMITER ',';

--将Hologres外部表数据dump到指定OSS
COPY (select * from foreign_holo_test LIMIT 20) TO PROGRAM 'hg_dump_to_oss --AccessKeyId <access id> --AccessKeySecret <access key> --Endpoint oss-cn-hangzhou-internal.aliyuncs.com --BucketName hologres-demo --DirName holotest/ --BatchSize 3000' (DELIMITER ',', HEADER true);

--跨region dump到指定OSS
COPY (select * from holo_test_1 LIMIT 20) TO PROGRAM 'hg_dump_to_oss --AccessKeyId <access id> --AccessKeySecret <access key> --Endpoint oss-cn-beijing-internal.aliyuncs.com --BucketName hologres-demo --DirName holotest/ --BatchSize 3000' (DELIMITER ',', HEADER true, FORMAT CSV);
```

**说明：** Hologres支持跨地域导出数据至指定的OSS。例如，可以导出杭州地域的实例查询出的数据至北京地域的OSS。

## 常见问题

常见的报错内容及解决方法如下：

-   `ERROR: syntax error at or near ")"LINE 1: COPY (select 1,2,3 from ) TO PROGRAM 'hg_dump_to_oss2 --Acce...`

    输入的query有误，请检查对应的查询语句。

-   `DETAIL: child process exited with exit code 255`

    选择的OSS网络类型有误。如果您使用的是公共云，请选择**经典网络**。

-   `DETAIL: command not found`

    您需要配置`DUMP TO OSS`的**program**为hg\_dump\_to\_oss，否则会出现该报错。

-   `ERROR: program "hg_dump_to_oss ...” failed DETAIL: child process exited with exit code 101`

    输入的AccessKeyId不合法，请使用当前账号的AccessKey ID。

-   `ERROR: program "hg_dump_to_oss ...” failed DETAIL: child process exited with exit code 102`

    输入的AccessKeySecret不合法，请使用当前账号的AccessKey Secret。

-   `ERROR: program "hg_dump_to_oss ...” failed DETAIL: child process exited with exit code 103`

    输入的Endpoint不合法，请确认对应OSS经典网络的Endpoint。

-   `ERROR: program "hg_dump_to_oss ...” failed DETAIL: child process exited with exit code 104`

    输入的BucketName不合法，请确认对应的Bucket名称。

-   `ERROR: program "hg_dump_to_oss ..." failed DETAIL: child process exited with exit code 105`

    缺少参数，请对照参数说明，检查必选参数是否均已配置。

-   `ERROR: program "hg_dump_to_oss ...” failed DETAIL: child process exited with exit code 255`

    一般情况下是由于holo server与指定的OSS网络不通导致该报错，可以更换OSS域名（例如：OSS网络类型选择经典网络）。更多关于OSS的域名信息，请参见[访问域名和数据中心](/intl.zh-CN/开发指南/访问域名（Endpoint）/访问域名和数据中心.md)。


