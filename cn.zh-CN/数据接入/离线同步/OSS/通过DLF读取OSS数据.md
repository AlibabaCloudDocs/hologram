---
keyword: [Hologres, DLF]
---

# 通过DLF读取OSS数据

本文将为您介绍在Hologres中如何通过DLF读取OSS数据。

-   您已开通Hologres实例并连接开发工具，并保证Hologres实例为V0.10及以上版本。本文以连接HoloWeb为例，详请参见[HoloWeb快速入门](/cn.zh-CN/快速入门/HoloWeb快速入门.md)。
-   您已开通OSS并上传数据，详请参见[开始使用OSS](/cn.zh-CN/快速入门/开始使用OSS.md)。
-   您已开通OSS，详请参见[快速入门]()。

阿里云数据湖构建（Data Lake Formation，DLF）是一款全托管的快速帮助用户构建云上数据湖的服务，产品提供了云上数据湖统一的权限管理、数据湖元数据管理和元数据自动抽取能力，更多关于DLF介绍请参见[DLF产品简介]()。

DLF使用阿里云对象存储（Object Storage Service，OSS）作为云上数据湖的统一存储，从Hologres V0.10版本开始，支持以外表方式通过DLF读取OSS中的数据，方便快捷，降低开发的复杂度和运维成本。

以外部表方式通过DLF访问OSS的数据，外部表只做字段映射，不真正存储数据，数据还是存储在OSS，相关原理请参见[PostgreSQL FDW](https://www.postgresql.org/docs/11/postgres-fdw.html)。

## 使用限制

通过DLF读取OSS数据的使用限制具体如下：

-   该函数仅Hologres V0.10及以上版本支持，请前往Hologres管控台实例详情页查看当前版本，如果您是V0.10以下的版本，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)升级实例。
-   使用该功能之前，需要实例的Superuser执行以下语句创建extension才可以使用。extension针对整个DB生效，一个DB只需执行一次，新建DB需要再次执行。

    ```
    create extension dlf_fdw;
    ```

-   当前仅支持读取同一个地域的数据，暂不支持跨地域读取。
-   目前支持的OSS文件格式为：Text（CSV）、SquenceFile、Parquet和ORC格式。
-   创建server时，DLF和OSS的网络类型都需要使用经典网络。

## 通过DLF读取OSS数据

在开始之前，您需要在DLF中准备元数据表，并保证该表中已抽取数据，详请参见[元数据管理]()。在Hologres中以外部表方式通过DLF访问OSS的数据操作步骤如下：

1.  创建extension

    在Hologres中由Superuser在DB中执行以下语句创建extension，用于开启通过DLF读取OSS数据的功能。该操作针对整个DB生效，一个DB只需执行一次。

    ```
    create extension if not exists dlf_fdw;
    ```

2.  创建server

    创建完extension之后，您需要创建server用于Hologres连接DLF和OSS，需要使用经典网络连接。

    -   语法示例

        ```
        create server if not exists <servername> foreign data wrapper dlf_fdw options
        (
            dlf_region 'cn-<region>',
            dlf_endpoint '<DLF经典网络>',
            oss_endpoint '<OSS经典网络>'
        );
        ```

    -   参数说明

        |参数|说明|示例|
        |--|--|--|
        |server\_name|自定义的server名称。|oss\_server|
        |endpoint|        -   DLF的endpoint经典网络，具体格式如下：`dlf-share.cn-<region>.aliyuncs.com`。
        -   OSS的endpoint需要使用OSS的经典网络。更多关于OSS的endpoint说明，请参见[获取OSS内网地址](/cn.zh-CN/开发指南/访问域名（Endpoint）/ECS实例通过OSS内网地址访问OSS资源.md)。
|        -   DLF

            ```
dlf-share.cn-shanghai.aliyuncs.com
            ```

        -   OSS

            ```
oss-cn-shanghai-internal.aliyuncs.com
            ``` |

    -   使用示例

        示例在上海区域创建一个名为dlf\_server的server：

        ```
        create server if not exists dlf_server foreign data wrapper dlf_fdw options
        (
            dlf_region 'cn-shanghai',
            dlf_endpoint 'dlf-share.cn-shanghai.aliyuncs.com', --上海DLF经典网络地址
            oss_endpoint 'oss-cn-shanghai-internal.aliyuncs.com'--OSS经典网络地址
        );
        ```

3.  （可选）创建用户映射

    默认情况下，使用当前用户的AccessKey ID和AccessKey Secret访问DLF元数据和读取表数据的操作。当前阿里云账号的AccessKey ID和AccessKey Secret，获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。如果不希望使用默认账号，也可以通过如下语句指定用户映射，查询时将使用该账号访问数据，请确保该账号有对应数据的查询权限。详细原理请参见[postgres create user mapping](https://www.postgresql.org/docs/11/sql-createusermapping.html)。

    ```
    create user mapping for <账号uid> server hm_dlf_server options
    (
        dlf_access_id 'accessid', dlf_access_key 'accesskey',
        oss_access_id 'accessid', oss_access_key 'accesskey'
    );
    ```

    使用示例：

    ```
    --为当前用户创建用户映射
    create user mapping for current_user server hm_dlf_server options
    (
        dlf_access_id 'LTAI5txxx', dlf_access_key 'y8LUUyyy',
        oss_access_id 'LTAI5txxx', oss_access_key 'y8LUUyyy'
    );
    
    --为RAM用户123xxx创建用户映射
    create user mapping for "p4_123xxx" server hm_dlf_server options
    (
        dlf_access_id 'LIlY5txxx', dlf_access_key 'KsjkXKyyy',
        oss_access_id 'LIlY5txxx', oss_access_key 'KsjkXKyyy'
    );
    ```

4.  创建外部表

    server创建完成后，您可以在Hologres中使用[IMPORT FOREIGN SCHEMA](/cn.zh-CN/Hologres SQL/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md)语法创建外部表，用于读取DLF抽取的OSS数据。

    -   语法示例

        ```
        import foreign schema remote_schema
            [ { limit to | except } ( table_name [, ...] ) ]
            from server server_name
            into local_schema 
            [ options ( option 'value' [, ... ] ) ]
        ```

    -   参数说明

        |参数|说明|
        |--|--|
        |remote\_schema|DLF中创建的元数据库名。|
        |table\_name|DLF中创建的元数据表名。|
        |server\_name|Hologres中创建的server名。|
        |local\_schema|Hologres中的schema名。|
        |options|IMPORT FOREIGN SCHEMA中的option参数取值，详请参见[IMPORT FOREIGN SCHEMA](/cn.zh-CN/Hologres SQL/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md)。|

    -   使用示例

        创建一张外部表映射DLF元数据库dlfpro中元数据表dlf\_oss\_test的数据，该表位于Hologres中的public schema，并且检验是否存在该外部表，若存在，则对已有表更新。

        ```
        import foreign schema dlfpro limit to
        (dlf_oss_test) 
        from server dlf_server  into public options(if_table_exist 'update');
        ```

        将DLF元数据库dlfpro中所有的表都映射至Hologres的public schema，将会在Hologres中批量创建同名外部表。

        ```
        import foreign schema dlfpro 
        from server dlf_server  into public options(if_table_exist 'update');
        ```

5.  数据查询

    创建外部表成功后，可以直接查询外部表读取OSS中的数据。

    ```
    select * from dlf_oss_test;
    ```


## 数据类型映射

|DLF数据类型|Hologres数据类型|
|-------|------------|
|INT|INT|
|BIGINT|BIGINT|
|STRING|TEXT|
|BOOLEAN|BOOLEAN|
|DATE|DATE|
|TIMESTAMP|TIMESTAMP|
|FLOAT|FLOAT|
|DOUBLE|DOUBLE|
|DECIMAL|DECIMAL|
|CHAR\(n）|CHAR\(n\)|
|VARCHAR\(n\)|VARCHAR\(n\)|

