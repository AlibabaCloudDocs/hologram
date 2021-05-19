# 通过外部表直接访问OSS

本文将为您介绍如何通过创建外部表的方式加速查询OSS数据。

## 前提条件

-   已开通Hologres并连接开发工具，本文使用HoloWeb，请参见[HoloWeb快速入门](/cn.zh-CN/快速入门/HoloWeb快速入门.md)。
-   已开通OSS并准备好数据，请参见[开始使用OSS](/cn.zh-CN/快速入门/开始使用OSS.md)。
-   已进行OSS授权操作，通过外部表方式访问OSS数据，需要访问的账号有OSS的相关访问权限，否则即使创建外表成功了，也无法查询数据。OSS授权请参见[授权策略](/cn.zh-CN/SDK 示例/Java/存储空间/授权策略.md)。

## 背景信息

对象存储服务（Object Storage Service，OSS）是一种海量、安全、低成本、高可靠的云存储服务，适合存放任意类型的文件。Hologres通过与OSS打通，支持通过创建外部表的方式，无需导入导出就能直接加速查询OSS的数据（数据仍然存储在OSS，外部表只做字段映射）。

Hologres兼容Postgres，通过创建外部表的方式访问OSS数据的原理同Postgres FDW。更多关于原理的描述，请参见[Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html)。

## 使用限制

通过创建外部表直接访问OSS的使用限制具体如下：

-   该函数仅Hologres V0.10及以上版本支持，请前往Hologres管控台实例详情页查看当前版本，如果您是V0.10以下的版本，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)升级实例。
-   在Hologres创建外部表之前，需要执行以下语句创建extension才可以使用。创建extension需要实例的Superuser执行，该操作针对整个DB生效，一个DB只需执行一次。

    ```
    create extension oss_fdw;
    ```

-   目前只支持部分数据类型，具体包括：string、text、bigint、int、float、double、boolean、date。
-   目前查询数据不支持跳过头部N行。
-   如果您需要将Hologres数据导出到OSS，详请参见[使用COPY命令导出Hologres的数据至OSS](/cn.zh-CN/数据接入/离线同步/OSS/使用COPY命令导出Hologres的数据至OSS.md)。

## 创建外部表直接访问OSS数据

通过在Hologres中创建外部表的方式直接访问OSS操作步骤如下：

1.  创建extension

    在Hologres中创建外部表之前，需要Superuser在DB中执行以下语句创建extension。创建extension需要实例的Superuser执行，该操作针对整个DB生效，一个DB只需执行一次。

    ```
    create extension oss_fdw;
    ```

2.  创建server

    创建完extension之后，您需要创建server用来连接OSS。

    -   语法示例

        ```
        CREATE SERVER <server_name> FOREIGN DATA WRAPPER oss_fdw 
        OPTIONS (
          endpoint 'https://<oss经典网络>'
        );
        ```

    -   参数说明

        |参数|说明|示例|
        |--|--|--|
        |server\_name|自定义的server名称。|oss\_server|
        |endpoint|OSS的endpoint需要以`http://` 或者`https://`开头，并且使用OSS的经典网络。更多关于OSS的endpoint说明，请参见[获取OSS内网地址](/cn.zh-CN/开发指南/访问域名（Endpoint）/ECS实例通过OSS内网地址访问OSS资源.md)。|oss-cn-shanghai-internal.aliyuncs.com|

3.  创建外部表

    server创建完成后，您可以在Hologres中创建外部表直接查询OSS中的数据。您可以根据业务场景需求创建parquet类型和text类型的外部表。创建的表必须和实际存储的格式一致，否则会导致读取异常。

    -   语法示例

        parquet类型具体语句如下：

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

        text类型具体语句如下：

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

    -   参数说明

        |参数|说明|示例|
        |--|--|--|
        |tablename|外部表名称。|oss\_table|
        |col|字段名称。|id|
        |type|字段类型。|int|
        |server\_name|创建的server名称。|oss\_server|
        |dir\_url|OSS表的存储URL，需要指定到目录名称。您需要注意如下事项：        -   不支持指定到具体文件，只支持到目录。
        -   Hologres会读取orc目录下的所有文件解析，请您保证单独目录下的格式一致，否则读取数据会出现乱码。
        -   目录名称不能指定为`./oss-test/orc/region_zlib_dict.orc`，否则虽然创建表成功，但是读取的内容为空表。
|orc表的存储位置具体如下：        ```
oss://oss-test/orc/region_zlib_dict.orc
        ```

那么，指定当前表的dir为`./oss-test/orc/`。|
        |format|当前OSS存储的表的格式。当前支持的format包括：        -   parquet
        -   orc
        -   text
|parquet|
        |delimiter|text类型的分隔符。**说明：** 当format格式为 text类型时候，此项为必填项。

|,|

4.  查询外部表

    创建外表成功后，可以直接查询外部表。

    ```
    SELECT COUNT (*) FROM osstest;--查询表osstest中记录的行数。
    
    SELECT * FROM osstest limit 10;--查询表osstest中的数据并返回10条记录。
    ```


## 性能优化

目前Hologres通过外部表直接读取OSS数据，即将某个目录下的所有文件读取出来作为一个表，在做表切分的时候，建议直接将单个文件作为一个切分单元。建议您尽量控制每个文件的大小一致，以防止某个表过大导致计算资源倾斜影响整体性能。

