---
keyword: 创建交互式分析Hologres源表
---

# Hologres源表

Flink全托管产品（Flink Serverless）是基于Apache Flink构建的全托管产品，为您提供全托管的实时计算服务。本文为您介绍Flink全托管如何创建Hologres源表。

## 使用说明

Hologres源表使用批模式读取数据，即对全表数据仅扫描一次。扫描结束，消费即结束，扫描结束后输入的数据将不会被Hologres源表读取。

## DDL定义

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

## 使用示例

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

## 类型映射

Flink全托管与Hologres的数据类型映射，请参见[t1345987.dita\#concept\_1597919](/cn.zh-CN/Hologres SQL/数据类型/数据类型汇总.md)。

