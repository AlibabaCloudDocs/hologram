---
keyword: [Hologres, Binlog]
---

# 订阅Hologres Binlog

本文将会为您介绍如何订阅Hologres Binlog。

## 注意事项

在Hologres中订阅Hologres Binlog需要注意如下事项：

-   仅Hologres V0.9及以上版本支持订阅Hologres Binlog，如果您的实例是V0.9以下版本，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)或加入在线支持钉钉群申请升级实例。
-   Hologres支持单表级别的Binlog功能，支持行存表和列存表。当前订阅Hologres Binlog支持的情况如下表：

    |Flink分类|Hologres行存表Binlog|Hologres 列存表Binlog|
    |-------|-----------------|------------------|
    |实时计算Blink|支持|支持|
    |全托管Flink|VVP-2.4支持|VVP-2.4支持|
    |开源Flink|Flink 1.12开始支持|Flink 1.12支持|


## 开启Binlog

-   功能简介

    Hologres中，Binlog功能默认关闭，您可以通过设置表属性binlog.level和binlog.ttl开启该功能。如果您对表格的更新比较频繁，理论上列存表开启Binlog功能的成本要大于行存表。因此，建议您使用行存表开启Binlog功能。

-   使用限制

    已存在的表无法修改表属性开启Binlog，需重新建表。

    Blink消费Hologres Binlog暂不支持Hologres的timestamp类型。在Hologres建表时，请使用timestamptz类型。

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


## Binlog格式说明

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

## Blink实时消费Binlog

实时计算Blink 3.7及以上版本，支持Hologres Connector实时消费Binlog，开启方法如下：

1.  使用DDL语句创建源表
    -   使用语法

        ```
        create table test_message_src_binlog_table(
          hg_binlog_lsn BIGINT,
          hg_binlog_event_type BIGINT,
          hg_binlog_timestamp_us BIGINT,
          id INTEGER,
          title VARCHAR,
          body VARCHAR
        ) with (
          type = 'hologres',
          `endpoint` = 'ip:port',--Hologres的vpc网络地址
          `username` = 'xxxx',--当前账号的Access ID
          `password` = 'xxxx',--当前账号的Access Key
          `dbname` = 'xxxx',--Hologres的DB名
          `tablename` = 'xxxx',--Hologres的表名
          `binlog` = 'true',
          `binlogMaxRetryTimes` = '10',
          `binlogRetryIntervalMs` = '500',
          `binlogBatchReadSize` = '256'
        );
        ```

    -   参数说明

        在语法示例中，hg\_binlog\_xxx开头的三个字段表示Binlog的系统字段，命名和类型不支持修改。其余字段需要和用户字段一一对应，且必须为小写。更多参数说明如下表所示：

        |参数名称|是否必填|说明|
        |----|----|--|
        |type|是|源表类型，值填写为hologres。|
        |endpoint|是|Hologres对应VPC的区域。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)，获取区域和端口信息。|
        |username|是|当前阿里云账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。|
        |password|是|当前阿里云账号的AccessKey Secret。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。|
        |dbname|是|读取的Hologres DB名称。|
        |tablename|是|读取的表名称。|
        |binlog|是|是否为binlog source。如果需要消费，需要将binlog source设置为true。|
        |binlogMaxRetryTimes|否|读取binlog出错重试次数，默认为60次。|
        |binlogRetryIntervalMs|否|读取binlog出错重试间隔，默认为2000ms。|
        |binlogBatchReadSize|否|读取binlog批量大小，默认为16个。|

2.  配置Binlog并发

    Binlog订阅的并发等于Hologres中Table的shard个数，请执行如下语句查看shard数。其中，`<tablename>`请替换为您实际的Table名称。Binlog并发建议执行计划配置，将其并发数与Binlog对应的Hologres中Table的shard数保持一致。

    ```
    select tg.property_value from hologres.hg_table_properties tb join hologres.hg_table_group_properties tg on tb.property_value = tg.tablegroup_name where tb.property_key = 'table_group' and tg.property_key = 'shard_count' and table_name = '<tablename>';
    ```


