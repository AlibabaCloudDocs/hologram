---
keyword: [创建Hologres结果表, 实时写入]
---

# Hologres结果表

Hologres与实时计算Blink独享模式（原产品线）深度融合，支持使用Connector的方式写入数据至Hologres结果表，您可以立即查询写入的数据。本文为您介绍实时计算Blink独享模式（原产品线）如何写入数据至Hologres结果表。

## 使用限制

-   不同Blink独享模式的版本开发语义不同，在使用之前，请先确定Blink独享模式的版本，并根据版本示例使用。
-   请确保开通的实时计算与Hologres地域一致，以免连接失败。
-   Blink独享模式3.6之前的版本未内置Hologres Connector，实时写入数据至Hologres需要引用JAR文件，您可以[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)[提交工单](https://workorder-intl.console.aliyun.com/)或通过Hologres交流群（钉钉群号：32314975）获取。

    **说明：** 建议您升级至3.6及以上的版本进行作业。

-   Blink独享模式3.7版本支持自动创建Hologres分区表，但是您需要在作业中配置`createparttable='true'`。同时，使用分区表的注意事项如下：
    -   Hologres当前仅支持List分区。
    -   创建分区表时，需要显示指定的分区列。目前仅支持text和int4类型的分区列，并且分区的值不能包含短划线（-），例如`2020-09-12`。
    -   如果分区表配置了主键（pk），则分区列必须是pk的一部分。
    -   创建分区子表时，子表分区列的值必须为固定值。
    -   写入分区子表的数据对应的分区列值，必须与子表创建时定义的值严格匹配，否则会报错。
    -   当前不支持DEFAULT分区功能。
-   当导入数据的Hologres目标表设置了主键，实时写入的默认语义不会按照主键进行更新，后续导入的主键数据如果重复，则会被丢弃。
-   Hologres为异步写入数据，您在进行作业时需要添加`blink.checkpoint.fail_on_checkpoint_error=true`配置，当作业发生异常时才会触发Failover。Blink3.7.6及以上版本不需要添加该参数。

## DDL语义

创建Hologres结果表的语句如下。

```
create table Hologres_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  type='hologres',
  dbname='<yourDbname>', --Hologres的数据库名称。
  tablename='<yourTablename>', --Hologres用于接收数据的表名称。
  username='<yourUsername>', --当前阿里云账号的AccessKey ID。
  password='<yourPassword>', --当前阿里云账号的AccessKey Secret。
  endpoint='<yourEndpoint>'); --当前Hologres实例VPC网络的Endpoint。
```

## WITH参数

|参数|描述|示例|
|--|--|--|
|type|结果表的类型。 固定值为hologres。|hologres|
|endpoint|Hologres实例的VPC网络地址。

进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)，在目标实例详情页的**实例配置**获取Endpoint。Endpoint需包含端口号，格式为ip:port。

|demo-cn-hangzhou-vpc.hologres.aliyuncs.com:80|
|username|AccessKey ID 您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。

|xxxxm3FMWaxxxx|
|password|AccessKey Secret 您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。

|xxxxm355fffaxxxx|
|dbname|当前Hologres的数据库名称。|Holodb|
|tablename|当前Hologres数据库的表名称。|blink\_test|
|arraydelimiter|Hologres Sink支持将一个STRING字段按照field\_delimiter切分为数组导入Hologres。默认值为\\u0002。

|\\u0002|
|mutatetype|数据写入模式，详情请参见[交互式分析Hologres结果表](/intl.zh-CN/Flink全托管/Flink SQL参考/DDL语句/创建数据结果表/交互式分析Hologres结果表.md)。默认值为insertorignore。

|insertorignore|
|ignoredelete|是否忽略回撤消息。-   true：忽略回撤消息。
-   false：不忽略回撤消息。

**说明：** 该参数仅在使用流式语义时生效。

默认为false。

通常Flink的Groupby会产生回撤消息，回撤消息传输到Hologres Connecor时会产生Delete请求。

|false|
|partitionrouter|是否写入分区表。-   true：写入分区表。
-   false：不写入分区表。

默认为false。

|false|
|createparttable|当写入分区表时，是否根据分区值自动创建分区表。Blink独享 3.7及以上版本支持该功能。默认值为false。

**说明：** 请您谨慎使用该功能，确保分区值不会出现脏数据，从而导致创建了错误的分区表。

|false|

**说明：** arraydelimiter、mutatetype、ignoredelete、partitionrouter及createparttable参数未在DDL示例语句中展示，如果您在实际应用中需要使用相应参数，可参考上述表格中的参数描述。

## 实时写入数据至Hologres普通结果表

1.  Hologres创建表。

    在Hologres中创建一张用于接收数据的表。示例建表SQL语句如下。

    ```
     create table blink_test (a int, b text, c text, d float8, e bigint);
    ```

2.  创建实时计算作业。

    1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c0j.14094430.1053885.btn5.380276feCFOVgD)[实时计算控制台](https://account.alibabacloud.com/login/login.htm?oauth_callback=http://stream-ap-southeast-3.console.aliyun.com/)。

    2.  创建实时计算作业。

        -   实时计算Blink 3.6及以上版本已支持Hologres数据源，您可以直接调用，示例SQL语句如下。

            ```
            create table randomSource (a int, b VARCHAR, c VARCHAR, d DOUBLE, e BIGINT) with (type = 'random');
            
            create table test (
              a int,
              b VARCHAR,
              c VARCHAR,
              PRIMARY KEY (a)
            ) with (
              type = 'hologres',
              `endpoint` = '$ip:$port', --当前Hologres的VPC网络地址以及端口号。
              `username` = '当前阿里云账号的AccessKey ID',
              `password` = '当前阿里云账号的AccessKey Secret',
              `dbname` = '当前Hologres的数据库名称',
              `tablename` = 'blink_test'--当前Hologres接收数据的表名称。
            );
            
            insert
              into test
            select
              a,b,c
            from
              randomSource;
            ```

        -   实时计算Blink 3.6以下的版本不支持Hologres数据源，您需要使用Hologres Connetcor写入数据，并且需要在作业中引用JAR文件。请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)[提交工单](https://workorder-intl.console.aliyun.com/)或通过Hologres交流群（钉钉群号：32314975）获取相应JAR文件。示例SQL语句如下。

            ```
            create table randomSource (a int, b VARCHAR, c VARCHAR, d DOUBLE, e BIGINT) with (type = 'random');
            create table test (
              a int,
              b VARCHAR,
              c VARCHAR,
              PRIMARY KEY (a)
            ) with (
              type = 'custom',
              tableFactoryClass = 'com.alibaba.blink.connectors.hologres.table.factory.HologresTableFactory',
              `endpoint` = '$ip:$port', --当前Hologres的VPC网络地址以及端口号。
              `username` = '当前阿里云账号的AccessKey ID',
              `password` = '当前阿里云账号的AccessKey Secret',
              `dbname` = '当前Hologres的数据库名称',
              `tablename` = 'blink_test' --当前Hologres接收数据的表名称。
            );
            
            insert
              into test
            select
              a,b,c
            from
              randomSource;
            ```

3.  上线作业。

    1.  完成新建作业后，单击编辑框的**语法检查**，如果显示**成功**，则表明语法正确。
    2.  单击**保存**保存作业。
    3.  单击**上线**，提交作业至生产环境。根据业务情况填写作业的上线配置。

        ![4](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5367359951/p84223.png)

4.  启动作业。

    提交作业至生产环境后，您需要手动启动作业。

    在**阿里实时计算开发平台**页面顶部菜单栏右侧，单击**运维**，跳转至运维界面，选择需要启动的作业，单击右上角的**启动**。

    ![6](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5367359951/p84227.png)

5.  Hologres实时查询数据。

    查询Hologres中用于接收数据的表，就可以实时获取到已写入的数据。示例查询SQL语句如下。

    ```
    select * from blink_test;
    ```


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

## 实时写入数据至Hologres的分区结果表

Hologres支持通过调用实时数据API接口，直接将数据写入分区父表中，对应的分区数据将会自动路由至分区子表。您可以直接写入数据至分区表。实时数据API的描述，详情请参见[实时数据API](/intl.zh-CN/数据接入/实时写入/实时计算Flink版/Blink独享集群（原产品线）/实时数据API.md)。

使用限制如下：

-   Hologres当前版本仅支持List分区。
-   创建分区表时，需要显示指定的分区列，分区列的类型仅支持text和 int4。
-   如果设置了主键，分区列必须为主键的一部分。
-   创建分区子表时，子表分区列的值必须为固定值。
-   写入分区子表的数据对应的分区列值，必须严格与创建子表时定义的值匹配，否则会报错。
-   Hologres当前不支持默认分区。

1.  Hologres创建分区表。

    在Hologres中创建一张用于接收数据的分区表，并创建对应的分区子表。示例建表SQL语句如下。

    ```
    --创建分区父表test_message和对应的分区子表。
    
    drop table if exists test_message;
    
    begin;
    create table test_message (
     "bizdate" text NOT NULL,
     "tag" text NOT NULL,
     "id" int4 NOT NULL,
     "title" text NOT NULL,
     "body" text,
    PRIMARY KEY (bizdate,tag,id)
    )
    PARTITION BY LIST (bizdate);
    commit;
    ```

    **说明：**

    -   执行命令时，`${bizdate}`参数需要替换为实际值。
    -   Blink独享模式3.7版本才支持自动创建分区，如果您使用的是Blink独享模式3.7以下的版本，需要在Hologres中提前创建分区子表，否则会导入数据失败。
2.  Blink独享创建作业。

    在Blink独享模式中创建作业的示例语句如下。

    **说明：** 以下示例适用于独享在Blink独享模式3.7及以上版本。如果您使用的是在Blink独享模式3.7以下版本，请升级至3.7及以上版本，或者删除自动创建分区子表的配置``createparttable` = 'true'`。

    ```
    create table test_message_src(
      tag VARCHAR,
      id INTEGER,
      title VARCHAR,
      body VARCHAR
    ) with (
      type = 'random',
      `interval` = '10',
      `count` = '100'
    );
    
    create table test_message_sink (
      bizdate VARCHAR,
      tag VARCHAR,
      id INTEGER,
      title VARCHAR,
      body VARCHAR
    ) with (
      type = 'hologres',
      `endpoint` = '$ip:$port', --Hologres实例的VPC网络地址。
      `username` ='<AccessID>', --当前阿里云账号的AccessKey ID。
      `password` = '<AccessKey>', --当前阿里云账号的AccessKey Secret。
      `dbname` = '<DBname>', --当前Hologres的数据库名称。
      `tablename` = '<Tablename>', --当前Hologres数据库的表名称。
      `partitionrouter` = 'true', --写入数据至Hologres的分区表。
      `createparttable` = 'true', --自动创建Hologres的分区子表。
    );
    
    insert into test_message_sink select * from test_message_src;
    ```

3.  上线并启动作业。

    请参考实时写入数据至Hologres结果表章节中的上线作业和启动作业步骤。

4.  Hologres实时查询数据。

    查询Hologres中用于接收数据的表，就可以实时获取到已写入的数据。示例查询SQL语句如下。

    ```
    select * from test_message;
    select * from test_message where bizdate = '20200327';
    ```


## 数据类型映射

Blink独享与Hologres的数据类型映射，请参见[t1345987.dita\#concept\_1597919](/intl.zh-CN/SQL参考/数据类型/数据类型汇总.md)。

