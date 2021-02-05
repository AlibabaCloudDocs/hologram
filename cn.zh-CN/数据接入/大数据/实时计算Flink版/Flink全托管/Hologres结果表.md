---
keyword: [创建Hologres结果表, 实时写入数据]
---

# Hologres结果表

Flink全托管产品（Flink Serverless）是基于Apache Flink构建的全托管产品，为您提供全托管的实时计算服务。本文为您介绍Flink全托管如何实时写入数据至Hologres结果表。

## 使用限制

Flink全托管2.12版本支持自动创建Hologres分区表，但是您需要在作业中配置`createparttable='true'`。同时，分区值中不能包含减号（-），例如2020-12-09。

## DDL定义

创建Hologres结果表的DDL语句如下。

```
create table hologres_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',  --Hologres的数据库名称。
  'tablename'='<yourTablename>',  --Hologres用于接收数据的表名称。
  'username'='<yourUsername>',  --当前阿里云账号的AccessKey ID。
  'password'='<yourPassword>',  --当前阿里云账号的AccessKey Secret。
  'endpoint'='<yourEndpoint>',  --当前Hologres实例VPC网络的Endpoint。);
```

**说明：** Flink全托管不支持在结果表中定义计算列。

With参数的描述如下表所示。

|参数|描述|是否必填|
|--|--|----|
|connector|结果表类型。固定值为hologres。

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
|field\_delimiter|Hologres Sink支持将一个STRING字段按照field\_delimiter切分为数组导入Hologres。默认值为"\\u0002"。

|否|
|mutatetype|数据写入模式，详情请参见[流式语义](#section_yce_507_nhr)。默认值为insertorignore。

|否|
|ignoredelete|是否忽略回撤消息。取值如下：-   false，不忽略。
-   true，忽略。

通常Flink的Groupby会产生回撤消息，回撤消息传输到Hologres Connector时会产生Delete请求。

默认值为false。

**说明：** 仅在使用流式语义时生效。

|否|
|partitionrouter|是否写入分区表。取值如下：-   false，不写入。
-   true，写入。

默认值为false。

|否|
|createparttable|当写入分区表时，是否根据分区值自动创建分区表。仅Flink全托管2.1.X及以上版本支持自动创建分区表。取值如下：-   false，不自动创建。
-   true，自动创建。

默认值为false。

**说明：** 请您谨慎使用该功能，确保分区值不会出现脏数据，导致创建了错误的分区表。

|否|

## 流式语义

流处理，也称为流数据或流事件处理，即对一系列无界数据或事件连续处理。执行流数据或流事件处理的系统通常允许您指定一种可靠性模式或处理语义，保证整个系统处理数据的准确性，因为网络或设备故障等可能会导致数据丢失。

根据Hologres Sink的配置和Hologres表的属性，流式语义分为以下两种：

-   Exactly-once（仅一次）：即使在发生各种故障的情况下，系统只处理一次数据或事件。
-   At-least-once（至少一次）：如果在系统完全处理之前丢失了数据或事件，则从源头重新传输，因此可以多次处理数据或事件。如果第一次重试成功，则不进行后续重试。

在Hologres结果表中使用流式语义，您需要注意以下几点：

-   如果Hologres物理表未设置主键，则Hologres Sink使用At-least-once语义。
-   如果Hologres物理表已设置主键，则Hologres Sink通过主键确保使用Exactly-once语义。当同主键数据出现多次时，您需要设置mutatetype参数确定更新结果表的方式，mutatetype取值如下：

    -   insertorignore（默认值）：保留首次出现的数据，忽略后续所有数据。
    -   insertorreplace：整行替换已有数据。
    -   insertorupdate：替换部分已有数据。例如一张表有a、b、c和d四个字段，a是主键PK（Primary Key），写入Hologres时只写入a和b两个字段，在主键重复的情况下，系统只会更新b字段，c和d保持不变。
    **说明：**

    -   当mutatetype设置为insertorupdate或insertorreplace时，系统根据主键更新数据。
    -   Flink全托管定义的结果表中的数据列数不一定要和Hologres物理表的列数一致，您需要保证缺失的列没有非空约束，即列值可以为Null，否则会报错。
-   默认情况下，Hologres Sink只能向一张表导入数据。如果导入数据至分区表的父表，即使导入成功，也会查询数据失败。您可以设置参数partitionrouter为true，开启自动将数据路由到对应分区表的功能。注意事项如下：
    -   tablename参数需要填写为分区表父表的表名称。
    -   Connector不会自动创建分区表。如果您使用的是Connector 2.12及以上版本，可以设置createparttable参数为true自动创建分区，如果是2.12以下的版本，需要您提前手动创建需要导入数据的分区表，否则会导入失败。

## 如何使用宽表Merge/局部更新功能

对于常见的多个流的数据写入至一张Hologres宽表的场景，具体使用方法如下：

假设Hologres有一张宽表WIDE\_TABLE，有A、B、C、D、E几列，其中A字段是主键，Flink一个流包含数据A、B、C，另一个流包含数据A、D、E。

1.  使用Flink SQL声明两张Hologres结果表，其中一张表只声明字段A、B、C，另一张表只声明字段A、D、E，这两张表都映射至*WIDE\_TABLE*。
2.  两张结果表的mutatetype属性都设置成insertorupdate。
3.  两张结果表的ignoredelete属性都设置成true，防止回撤消息产生Delete请求。
4.  将两个流的数据分别Insert至两张结果表中。

该场景的具体使用限制如下：

1.  宽表必须有主键。
2.  每个流的数据都必须包含完整主键字段。
3.  列存表的宽表Merge场景在高RPS的情况下，CPU使用率会偏高，建议关闭表中字段的Dictionary encoding。

## 类型映射

Flink全托管与Hologres的数据类型映射，请参见[数据类型汇总](/cn.zh-CN/Hologres SQL/数据类型/数据类型汇总.md)。

