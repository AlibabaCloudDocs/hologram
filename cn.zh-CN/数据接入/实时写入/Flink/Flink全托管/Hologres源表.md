---
keyword: 创建交互式分析Hologres源表
---

# Hologres源表

Flink全托管产品（Flink Serverless）是基于Apache Flink构建的全托管产品，为您提供全托管的实时计算服务。本文为您介绍Flink全托管如何创建Hologres源表。

## 使用说明

-   Hologres源表默认使用批模式读取数据，即对全表数据仅扫描一次。扫描结束，消费即结束，扫描结束后输入的数据将不会被Hologres源表读取。
-   全托管Flink从VVP-2.4版本开始，支持实时消费Hologres的数据，请您升级全托管Flink至VVP-2.4版本。更多关于参数使用说明，请参见[Flink实时消费Binlog](#section_8vr_av0_xc6)。

## DDL定义

-   DDL语句

    创建Hologres源表的DDL语句如下。

    ```
    create table hologres_source(
      name varchar,
      age BIGINT,
      birthday BIGINT
    ) with (
      'connector'='hologres',
      'dbname'='<yourDbname>', --Hologres的数据库名称。
      'tablename'='<yourTablename>',  --Hologres用于接收数据的表名称。
      'username'='<yourAccessID>',  --当前阿里云账号的AccessKey ID。
      'password'='<yourAccessSecret>',  --当前阿里云账号的AccessKey Secret。
      'endpoint'='<vpc_ip:vpc_port>',  --当前Hologres实例VPC网络的Endpoint。
      'field_delimiter'='|' --该参数为可选参数。
    );
    ```

    **说明：** Flink全托管不支持在源表中定义计算列。

-   参数说明

    With参数的描述如下表所示。

    |参数|描述|是否必填|
    |--|--|----|
    |connector|源表类型。固定值为hologres。

|是|
    |dbname|Hologres的数据库名称。|是|
    |tablename|Hologres用于接收数据的表名称。|是|
    |username|当前阿里云账号的AccessKey ID。您可以登录[AccessKey 管理](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb)，获取AccessKey ID。

|是|
    |password|当前阿里云账号的AccessKey Secret。您可以登录[AccessKey 管理](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb)，获取AccessKey Secret。

|是|
    |endpoint|Hologres的VPC网络地址。您可以登录[Hologres管控台](https://hologram.console.aliyun.com/#/instance)，进入目标实例的详情页，在**实例配置**中获取Endpoint。Endpoint需包含端口号，格式为ip:port。

**说明：** 如果Flink与Hologres实例部署在同一个地域，请使用VPC网络的网络地址。如果在不同地域，则请使用公共网络的网络地址。

|是|
    |field\_delimiter|导出数据时，不同行之间使用的分隔符。默认值为"\\u0002"。

|否|
    |binlog|是否为binlog source，VVP 2.4版本开始支持，默认为false。如果需要消费，需要将binlog source设置为true。|否|
    |binlogMaxRetryTimes|读取binlog出错重试次数，默认为60次。|否|
    |binlogRetryIntervalMs|读取binlog出错重试间隔，默认为2000ms。|否|
    |binlogBatchReadSize|读取binlog批量大小，默认为16个。|否|
    |cdcMode|读取binlog时是否采用CDC模式，默认为false。详请参见[Flink实时消费Binlog](#section_8vr_av0_xc6)。|否|

-   使用示例

    创建Hologres的源表并导入Flink的数据，示例SQL语句如下。

    ```
    CREATE TEMPORARY TABLE hologres_source (
      name varchar, 
      age BIGINT,
      birthday BIGINT
    ) with (
      'connector'='hologres',
      'dbname'='<yourDbname>',
      'tablename'='<yourTablename>',
      'username'='<yourAccessID>',
      'password'='<yourAccessSecret>',
      'endpoint'='<yourEndpoint>',
      'field_delimiter'='|'  --该参数可选。
    );
    CREATE TEMPORARY TABLE blackhole_sink(
      name varchar,
      age BIGINT,
      birthday BIGINT 
    ) with (
      'connector'='blackhole'
    );
    INSERT INTO blackhole_sink
    SELECT 
       name, age, birthday
    from hologres_source;
    ```


## Flink实时消费Binlog

Flink VVP-2.4及以上版本，Hologres Connector支持实时消费Binlog，使用方法如下。

-   **开启Binlog**
    -   功能简介

        Hologres中，Binlog功能默认关闭，您可以通过设置表属性binlog.level和binlog.ttl开启该功能。如果您对表格的更新比较频繁，理论上列存表开启Binlog功能的成本要大于行存表。因此，建议您使用行存表开启Binlog功能。

    -   使用限制

        已存在的表无法修改表属性开启Binlog，需重新建表。

        Flink消费Hologres Binlog暂不支持Hologres的timestamp类型。在Hologres建表时，请使用timestamptz类型。

    -   使用示例

        您可以在创建表的时候设置表属性binlog.level和binlog.ttl开启Binlog功能。更多关于创建表的参数说明，请参见[CREATE TABLE](/cn.zh-CN/Hologres SQL/DDL/TABLE/CREATE TABLE.md)。

        ```
        begin;
        create table test_message_src(
          id int primary key, 
          title text not null, 
          body text);
        call set_table_property('test_message_src', 'orientation', 'row');--创建行存表test_message_src
        call set_table_property('test_message_src', 'clustering_key', 'id');--在id列建立聚簇索引
        call set_table_property('test_message_src', 'binlog.level', 'replica');--设置表属性开启Binlog功能
        call set_table_property('test_message_src', 'binlog.ttl', '86400');--binlog.ttl,Binlog的TTL，单位为秒
        commit;
        ```

-   **Binlog格式**

    Binlog字段由Binlog系统字段和用户Table字段组成，具体字段定义如下表。

    |字段名称|字段类型|说明|
    |----|----|--|
    |hg\_binlog\_lsn|BIGINT|Binlog的系统字段，表示Binlog序号。Shard内部单调递增不保证连续，不同Shard之间不保证唯一和有序。|
    |hg\_binlog\_event\_type|BIGINT|Binlog的系统字段，表示当前Record所表示的修改类型。|
    |hg\_binlog\_timestamp\_us|BIGINT|Binlog的系统字段，系统时间戳，单位为us。|
    |user\_table\_column\_1|用户自定义|用户Table字段。|
    |...|...|...|
    |user\_table\_column\_n|用户自定义|用户Table字段。|

    -   hg\_binlog\_event\_type有如下四种可能的取值：
        -   INSERT=5，表示当前Binlog为插入一条新的记录。
        -   DELETE=2，表示当前Binlog为删除一条已有的记录。
        -   BEFORE\_UPDATE=3，表示当前Binlog为一条已有记录其更新前的记录。
        -   AFTER\_UPDATE=7，表示当前Binlog为一条已有记录其更新后的记录。
    -   UPDATE操作会产生两条Binlog记录，分别为更新前和更新后的记录。订阅 Binlog功能会保证这两条记录是连续的且更新前的Binlog记录在前，更新后的 Binlog记录在后。
    -   用户字段的顺序与DDL定义的顺序一致。
-   **源表 DDL（非CDC模式）**

    该模式下Source消费的Binlog数据是作为普通的Flink数据传递给下游节点的，即所有数据都是作为Insert类型的数据，可以根据业务情况选择如何处理特定`hg_binlog_event_type`类型的数据，具体示例如下。

    ```
    create table test_message_src_binlog_table(
      hg_binlog_lsn BIGINT,
      hg_binlog_event_type BIGINT,
      hg_binlog_timestamp_us BIGINT,
      id INTEGER,
      title VARCHAR,
      body VARCHAR
    ) with (
      'connector'='hologres',
      'dbname'='<yourDbname>',
      'tablename'='<yourTablename>',
      'username'='<yourAccessID>',
      'password'='<yourAccessSecret>',
      'endpoint'='<yourEndpoint>',
      'binlog' = 'true',
      'binlogMaxRetryTimes' = '10',
      'binlogRetryIntervalMs' = '500',
      'binlogBatchReadSize' = '100'
    );
    ```

    在语法示例中，hg\_binlog\_xxx开头的三个字段表示Binlog的系统字段，命名和类型不支持修改。其余字段需要和用户字段一一对应，且必须为小写。

-   **源表 DDL（CDC模式）**

    该模式下Source消费的Binlog数据，将根据`hg_binlog_event_type`自动为每行数据设置准确的Flink RowKind类型（INTERT、DELETE、UPDATE\_BEFORE、UPDATE\_AFTER\)，这样就能完成表的数据的镜像同步，类似MySQL和Postgres的CDC功能，具体示例如下。

    ```
    create table test_message_src_binlog_table(
      id INTEGER,
      title VARCHAR,
      body VARCHAR
    ) with (
      'connector'='hologres',
      'dbname'='<yourDbname>',
      'tablename'='<yourTablename>',
      'username'='<yourAccessID>',
      'password'='<yourAccessSecret>',
      'endpoint'='<yourEndpoint>',
      'binlog' = 'true',
      'cdcMode' = 'true'
      'binlogMaxRetryTimes' = '10',
      'binlogRetryIntervalMs' = '500',
      'binlogBatchReadSize' = '100'
    );
    ```

-   配置Binlog并发

    Binlog订阅的并发等于Hologres中Table的shard个数，请执行如下语句查看shard数。其中，`<tablename>`请替换为您实际的Table名称。Binlog并发建议执行计划配置，将其并发数与Binlog对应的Hologres中Table的shard数保持一致。

    ```
    select tg.property_value from hologres.hg_table_properties tb join hologres.hg_table_group_properties tg on tb.property_value = tg.tablegroup_name where tb.property_key = 'table_group' and tg.property_key = 'shard_count' and table_name = '<tablename>';
    ```


## 数据类型映射

Flink全托管与Hologres的数据类型映射，请参见[t1345987.dita\#concept\_1597919](/cn.zh-CN/Hologres SQL/数据类型/数据类型汇总.md)。

