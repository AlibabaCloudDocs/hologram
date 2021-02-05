---
keyword: [创建Hologres维表, ]
---

# Hologres维表

本文为您介绍Blink独享模式如何创建Hologres维表。

## 使用限制

1.  建议使用**Hologres的行存表**，列存表对于点查场景性能开销较大。

    创建行存储表时必须设置主键，并且将主键配置为clustering key才可以工作，Hologres创建表的示例语句如下。

    ```
    begin;
    create table test(a int primary key, b text, c text[], d float8, e int8);
    call set_table_property('test', 'orientation', 'row');
    call set_table_property('test', 'clustering_key', 'a');
    commit;
    ```

2.  表的主键必须是Flink Join ON的字段，Flink Join ON的字段也必须是表的完整主键，两者必须完全匹配。
3.  Hologres Blink Connector的维表功能不支持一对多的输出

## DDL语义

创建Hologres维表的DDL语句如下。

```
CREATE TABLE rds_dim_table(
 id INT,
 len INT,
 content VARCHAR,
 PRIMARY KEY (id),
 PERIOD FOR SYSTEM_TIME --定义维表的变化周期，表明该表是一张会变化的表。
) with (
  type='hologres',
  'dbname'='<yourDbname>',  --Hologres的数据库名称。
  'tablename'='<yourTablename>', --Hologres用于接收数据的表名称。
  'username'='<yourUsername>', --当前阿里云账号的AccessKey ID。
  'password'='<yourPassword>', --当前阿里云账号的AccessKey Secret。
  'endpoint'='<yourEndpoint>' --当前Hologres实例VPC网络的Endpoint。
);
```

With参数的描述如下表所示。

|参数|描述|是否必选|
|--|--|----|
|type|维表类型。固定值为hologres。

|是|
|dbname|Hologres的数据库名称。|是|
|tablename|Hologres用于接收数据的表名称。|是|
|username|当前阿里云账号的AccessKey ID。您可以登录[AccessKey 管理](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb)，获取AccessKey ID。

|是|
|password|当前阿里云账号的AccessKey Secret。您可以登录[AccessKey 管理](https://ram.console.aliyun.com/manage/ak?spm=5176.2020520207.nav-right.dak.538b4c12VYbuIb)，获取AccessKey Secret。

|是|
|endpoint|Hologres的VPC网络地址。您可以登录[Hologres管控台](https://hologram.console.aliyun.com/#/instance)，进入目标实例的详情页，在**实例配置**中获取Endpoint。Endpoint需包含端口号，格式为ip:port。

|是|

## Cache参数

如果Hologres维表包含Cache参数，则可以参考如下参数描述。

|参数|描述|是否必选|示例值|
|--|--|----|---|
|cache|缓存策略|否|目前交互式分析Hologres维表支持以下三种缓存策略： -   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果未找到，则去物理维表中查找。

需要配置相关参数：缓存大小（cachesize）和缓存更新时间间隔（cachettlms）。

-   ALL：缓存维表里的所有数据。在Job运行前，系统会将维表中所有数据加载到Cache中，之后所有的维表查找数据都会通过Cache进行。如果在Cache中无法找到数据，则KEY不存在，并在Cache过期后重新全量加载Cache。

适用于远程表数据量小且MISS KEY（源表数据和维表JOIN时，ON条件无法关联）较多的场景。

需要配置相关参数：缓存更新时间间隔（cachettlms），更新时间黑名单（cachereloadtimeblackList）。


**说明：**

-   因为系统会异步加载维表数据，所以在使用CACHE ALL时，需要增加维表JOIN节点的内存，增加的内存大小为远程表数据量的两倍。
-   对于数据量比较大的维表，选择CACHE ALL时，可能会出现OOM或者Full GC耗时很久的情况，针对这个问题，可以选择以下两种解决方式：
    -   对于支持Cache All策略的维表，开启Partitionedjoin优化。

        -   3.6.0之前的版本，每个并发默认加载维表全量数据。
        -   3.6.0及之后的版本，CACHE ALL策略支持Partitionedjoin优化。
开启Partitionjoin优化后，每个并发只缓存自己并发所需要的数据。

    -   使用HBase或者RDS等Key-Value类型的维表。 |
|cachesize|缓存大小|否|当缓存策略选择LRU时，可以设置缓存大小，默认为10000行。|
|cachettmlss|缓存失效时间，单位为毫秒。|否|-   当缓存策略选择LRU时，可以设置缓存失效时间，默认不过期。
-   当缓存策略选择ALL时，缓存失效时间为缓存重新加载的间隔时间，默认不重新加载。 |
|partitionedjoin|根据分区查找数据|否|partitionedjoin参数的取值如下： -   false（默认值）
-   true |
|async|是否异步读取数据。|否|async参数的取值如下： -   false（默认值）
-   true |

## 使用示例

创建Hologres维表并导入数据的示例语句如下。

```
create table randomSource (a int, b VARCHAR, c VARCHAR) with (type = 'random');

create table test (
      a int,
    b VARCHAR,
    c VARCHAR,
    PRIMARY KEY (a, b), PERIOD FOR SYSTEM_TIME
) with (
  type = 'hologres',
  'dbname'='<yourDbname>', --Hologres的数据库名称。
  'tablename'='<yourTablename>', --Hologres用于接收数据的表名称。
  'username'='<yourUsername>', --当前阿里云账号的AccessKey ID。
  'password'='<yourPassword>', --当前阿里云账号的AccessKey Secret。
  'endpoint'='<yourEndpoint>' --当前Hologres实例VPC网络的Endpoint。
);

create table print_sink (
  a int,
  b VARCHAR
) with (type = 'print', `ignoreWrite` = 'false');

insert into print_sink
select randomSource.a, test.b from randomSource
LEFT JOIN test FOR SYSTEM_TIME AS OF PROCTIME()
on randomSource.a = test.a and randomSource.b = test.b;
```

## 数据类型映射

Blink独享与Hologres的数据类型映射，请参见[t1345987.dita\#concept\_1597919](/cn.zh-CN/Hologres SQL/数据类型/数据类型汇总.md)。

