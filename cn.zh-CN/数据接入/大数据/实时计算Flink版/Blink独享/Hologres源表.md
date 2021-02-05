---
keyword: [创建Hologres源表, 实时写入]
---

# Hologres源表

本文为您介绍在独享Blink中如何创建交互式分析Hologres源表。

## 使用说明

Hologres源表默认使用批模式读取数据，即对全表数据仅扫描一次。扫描结束，消费即结束，扫描结束后输入的数据将不会被Hologres源表读取。如果您需要使用实时消费Hologres的数据，请参见[订阅Hologres Binlog（Beta）](/cn.zh-CN/数据接入/大数据/实时计算Flink版/Blink独享/订阅Hologres Binlog（Beta）.md)。

## 使用限制

Hologres支持使用Holo-blink Connector将Hologres表作为源表读取数据并进行维表Join，其使用限制如下：

-   默认只能读取**行存储格式**的表数据。如果要读取列存储格式表的数据，需要配置`bulkread='true'`。
-   创建行存储表时，如果该表设置了主键，必须将主键配置为clustering key才能工作。Hologres创建源表的示例语句如下。

    ```
    begin;
    create table test(a int primary key, b text, c text[], d float8, e int8);
    call set_table_property('test', 'orientation', 'row');
    call set_table_property('test', 'clustering_key', 'a');
    commit;
    ```

-   Blink独享模式3.6以下的版本未支持Hologres数据源，您需要使用Hologres Connector，并引用相关JAR文件，才可以进行作业。请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)或者联系对应的技术支持获取JAR文件，并在作业中使用如下DDL语句创建Hologres源表。

## DDL定义

-   Blink独享模式3.6及以上版本支持Hologres数据源，其创建Hologres源表的DDL语句如下所示。

    ```
    CREATE TABLE holo_dim_table 
    (
        pk VARCHAR
        ,seller_id VARCHAR
        ,seller_bc_type VARCHAR
        ,seller_tag  VARCHAR
        ,PRIMARY KEY (pk)
    ) with (
        type = 'hologres',
        `endpoint` = '<yourEndpoint>',  --当前Hologres实例VPC网络的Endpoint。
        `username` = '<yourUsername>',  --当前阿里云账号的AccessKey ID。
        `password` = '<yourPassword>',  --当前阿里云账号的AccessKey Secret。
        `dbname` = '<yourDbname>',  --Hologres的数据库名称。
        `tablename` = '<yourTablename>',  --Hologres用于接收数据的表名称。
        `bulkread`='true'
    );
    ```


With参数的描述如下表所示。

|参数|描述|是否必填|
|--|--|----|
|type|数据源类型。默认为hologres。

|是|
|dbname|Hologres的数据库名称。|是|
|tablename|Hologres用于接收数据的表名称。|是|
|username|当前阿里云账号的AccessKey ID。您可以登录[AccessKey 管理](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb)，获取AccessKey ID。

|是|
|password|当前阿里云账号的AccessKey Secret。您可以登录[AccessKey 管理](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb)，获取AccessKey Secret。

|是|
|endpoint|Hologres的VPC网络地址。您可以登录[Hologres管控台](https://hologram.console.aliyun.com/#/instance)，进入目标实例的详情页，在**实例配置**中获取Endpoint。Endpoint需包含端口号，格式为ip:port。

|是|
|bulkread|参数取值如下：-   true：表示可以使用Hologres的列存储表作为源表。
-   false：表示只能使用Hologres的行存储表作为源表。

**说明：** 如果您需要使用Hologres的列存储表作为源表进行作业，则需要配置该参数为true。

|是|

## 数据类型映射

Blink独享的数据类型与Hologres的数据类型映射，请参见[t1345987.dita\#concept\_1597919](/cn.zh-CN/Hologres SQL/数据类型/数据类型汇总.md)。

