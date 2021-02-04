---
keyword: [创建Hologres维表, 实时写入数据]
---

# Hologres维表

Flink全托管产品（Flink Serverless）是基于Apache Flink构建的全托管产品，为您提供全托管的实时计算服务。本文为您介绍在Flink全托管中如何使用Hologres维表。

## 使用限制

1.  建议使用Hologres的行存表，列存表对于点查场景性能开销较大。

    创建行存储表时必须设置主键，并且将主键配置为clustering key时才可以工作，示例语句如下。

    ```
    begin;
    create table test(a int primary key, b text, c text, d float8, e int8);
    call set_table_property('test', 'orientation', 'row');
    call set_table_property('test', 'clustering_key', 'a');
    commit;
    ```

2.  表的主键必须是Flink Join ON的字段，Flink Join ON的字段也必须是表的完整主键，两者必须完全匹配。
3.  Hologres Blink Connector的维表功能不支持一对多的输出。

## DDL定义

创建Hologres维表的DDL语句如下。

```
CREATE TABLE hologres_dim(
 id INT,
 len INT,
 content VARCHAR
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',  --Hologres的数据库名称。
  'tablename'='<yourTablename>',  --Hologres用于接收数据的表名称。
  'username'='<yourUsername>',  --当前阿里云账号的AccessKey ID。
  'password'='<yourPassword>',  --当前阿里云账号的AccessKey Secret。
  'endpoint'='<yourEndpoint>'  --当前Hologres实例VPC网络的Endpoint。
);
```

With参数的描述如下表所示。

|参数|描述|是否必填|
|--|--|----|
|connector|维表类型。固定值为hologres。

|是|
|dbname|Hologres的数据库名称。|是|
|tablename|Hologres用于接收数据的表名称。|是|
|username|当前阿里云账号的AccessKey ID。您可以登录[AccessKey 管理](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb)，获取AccessKey ID。

|是|
|password|当前阿里云账号的AccessKey Secret。您可以登录[AccessKey 管理](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb)，获取AccessKey Secret。

|否|
|endpoint|Hologres的VPC网络地址。您可以登录[Hologres管控台](https://hologram.console.aliyun.com/#/instance)，进入目标实例的详情页，在**实例配置**中获取Endpoint。Endpoint需包含端口号，格式为ip:port。

**说明：** 如果Flink与Hologres实例部署在同一个地域，请使用VPC网络的网络地址。如果在不同地域，则请使用公共网络的网络地址。

|是|

## Cache参数

如果Hologres维表包含Cache参数，则可以参考如下参数描述。

|参数|描述|是否必填|
|--|--|----|
|cache|缓存策略Hologres仅支持以下两种缓存策略：

-   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果未找到，则去物理维表中查找。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。


|否|
|cacheSize|缓存大小选择`LRU`缓存策略后，可以设置缓存大小，默认值为10000行。

|否|
|cacheTTLMs|更新缓存的时间间隔。当选择`LRU`缓存策略后，可以设置缓存失效的超时时间，默认不过期。

|否|
|async|是否异步同步数据。取值如下：

-   true：表示异步同步数据。
-   false（默认值）：表示不进行异步同步数据。

|否|

## 使用示例

创建Hologres维表并接收Flink的数据，示例语句如下。

```
CREATE TEMPORARY TABLE datagen_source (
   a INT,
   b BIGINT,
   c STRING,
   proctime AS PROCTIME()
) with (
   'connector' = 'datagen'
);

CREATE TEMPORARY TABLE hologres_dim (
   a INT, 
   b VARCHAR, 
   c VARCHAR
) with (
   'connector' = 'hologres',
   ...
);

CREATE TEMPORARY TABLE blackhole_sink (
   a INT,
   b STRING
) with (
   'connector' = 'blackhole'
);

insert into blackhole_sink select T.a,H.b
FROM datagen_source AS T JOIN hologres_dim FOR SYSTEM_TIME AS OF T.proctime AS H ON T.a = H.a;
```

## 类型映射

Flink全托管与Hologres的数据类型映射，请参见[数据类型汇总](/cn.zh-CN/Hologres SQL/数据类型/数据类型汇总.md)。

