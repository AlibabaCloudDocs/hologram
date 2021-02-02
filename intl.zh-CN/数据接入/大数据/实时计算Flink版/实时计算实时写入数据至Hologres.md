---
keyword: [实时写入Hologres, 实时计算]
---

# 实时计算实时写入数据至Hologres

本文为您介绍如何通过创建实时计算作业实时写入数据至Hologres。

## 前提条件

-   开通Hologres实例并连接开发工具，详情请参见[psql快速入门](/intl.zh-CN/快速入门/psql快速入门.md)。
-   开通实时计算，详情请参见[t40807.md\#](/intl.zh-CN/独享模式/准备工作/开通服务和创建项目.md)。

**说明：**

-   请确保开通的实时计算与Hologres地域一致，以免连接失败。
-   Blink 3.6之前的版本未内置Hologres Connector，实时写入数据至Hologres需要引用JAR文件，您可以[提交工单](https://workorder-intl.console.aliyun.com/)或通过Hologres交流群（钉钉群号：32314975）获取。
-   当Hologres中接收数据的表已设置主键，默认按照主键更新实时写入的数据。
-   如果使用批处理方式导入数据，则需要设置BatchSize并使用HoloHub的Endpoint。

## 操作步骤

1.  创建Hologres表。

    在Hologres中创建一张用于接收数据的表。示例建表SQL语句如下。

    ```
     create table blink_test(
      a int primary key,
      b text,
      c text
    );
    ```

2.  创建实时计算作业。

    通过创建实时计算作业实时写入数据至Hologres的操作步骤如下：

    1.  新建作业。

        1.  登录[实时计算控制台](https://account.alibabacloud.com/login/login.htm?oauth_callback=http://stream-ap-southeast-3.console.aliyun.com/)。
        2.  1.  实时计算Blink 3.6及以上版本已成功支持Hologres数据源，您可以直接调用，示例SQL语句如下。

    ```
    create table randomSource (a int, b VARCHAR, c VARCHAR, d DOUBLE, e BIGINT) with (type = 'random');
    
    create table test (
      a int,
      b VARCHAR,
      c VARCHAR,
      PRIMARY KEY (a)
    ) with (
      type = 'hologres',
      `endpoint` = '$ip:$port', //当前Hologres的VPC网络地址以及端口号。
      `username` = '当前阿里云账号的AccessKey ID',
      `password` = '当前阿里云账号的AccessKey Secret',
      `dbName` = '当前Hologres的数据库名称',
      `tableName` = 'blink_test'//当前Hologres接收数据的表名称。
    );
    
    insert
      into test
    select
      a,b,c
    from
      randomSource;
    ```

2.  实时计算Blink 3.6以下的版本不支持Hologres数据源，您需要使用Hologres Connetcor写入数据，示例SQL语句如下。

    **说明：** Blink 3.6之前的版本未内置Hologres Connector，实时写入数据至Hologres需要引用JAR文件，您可以[提交工单](https://workorder-intl.console.aliyun.com/)或通过Hologres交流群（钉钉群号：32314975）获取。

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
      `endpoint` = '$ip:$port', //当前Hologres的VPC网络地址以及端口号。
      `username` = '当前阿里云账号的AccessKey ID',
      `password` = '当前阿里云账号的AccessKey Secret',
      `dbName` = '当前Hologres的数据库名称',
      `tableName` = 'blink_test' //当前Hologres接收数据的表名称。
    );
    
    insert
      into test
    select
      a,b,c
    from
      randomSource;
    ```

            With参数如下表所示。

            |参数|描述|示例|
            |--|--|--|
            |type|源表的类型。 **说明：** Blink 3.6及以上版本使用hologres，3.6以下的版本使用custom。

|hologres|
            |endpoint|$ip:$port当前Hologres的VPC网络地址以及端口号。

进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)**实例配置**获取Endpoint。

|demo-cn-hangzhou-vpc.hologres.aliyuncs.com:80|
            |username|AccessKey ID 您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。

|xxxxm3FMWaxxxx|
            |password|AccessKey Secret 您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。

|xxxxm355fffaxxxx|
            |dbName|当前Hologres的数据库名称。|Holodb|
            |tableName|当前Hologres数据库的表名称。|blink\_test|

    2.  上线作业。

        1.  完成新建作业后，单击编辑框的**语法检查**，如果显示**成功**，则表明语法正确。
        2.  单击**保存**保存作业。
        3.  单击**上线**，提交作业至生产环境。根据业务情况填写作业的上线配置。

            ![4](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5367359951/p84223.png)

    3.  启动作业。

        提交作业至生产环境后，您需要手动启动作业。

        在**阿里实时计算开发平台**页面顶部菜单栏右侧，单击**运维**，跳转至运维界面，选择需要启动的作业，单击右上角的**启动**。

        ![6](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5367359951/p84227.png)

3.  Hologres实时查询。

    查询Hologres中用于接收数据的表，就可以实时获取到已写入的数据。示例查询SQL语句如下。

    ```
    select * from blink_test;
    ```


## Hologres读取Blink数据并进行维表JOIN

使用Holo Blink Connector将Hologres表作为源表读取Blink数据并进行维表JOIN。

**说明：**

-   只有使用行存储格式的表支持将Hologres表作为源表读取Blink数据并进行维表JOIN。
-   如果创建的行存储的表有主键，必须设置主键为Clustering Key，才能正常作业。

示例Hologres创建表的SQL语句如下。

```
begin;
create table test(a int primary key, b text, c text[], d float8, e int8);
call set_table_property('test', 'orientation', 'row');
call set_table_property('test', 'clustering_key', 'a');
commit;
```

Blink维表JOIN示例如下：

-   实时计算Blink 3.6及以上版本支持Hologres数据源，您可以直接实时写入，示例SQL语句如下。

    ```
    CREATE TABLE holo_dim_table 
    (
        pk VARCHAR
        ,seller_id VARCHAR
        ,seller_bc_type VARCHAR
        ,seller_tag  VARCHAR
        ,PRIMARY KEY (pk)
        ,PERIOD FOR SYSTEM_TIME 
    ) with (
        type = 'hologres',
        `endpoint` = '',
        `userName` = '',
        `password` = '',
        `dbName` = 'hologres-test',
        `tableName` = 'test',
        `cache`='LRU',
        `cacheSize`='100000',
        `cacheTTLMs`='1800000',
        `asyncResultOrder`='unordered',
        `asyncTimeoutMs`='900000',
        `asyncCapacity`='1200',
        `async` = 'true'
    );
    ```

-   实时计算Blink 3.6以下的版本不支持Hologres数据源，您需要使用Hologres Connetcor导入数据，并引用相关JAR文件，示例SQL语句如下。

    ```
    CREATE TABLE holo_dim_table 
    (
        pk VARCHAR
        ,seller_id VARCHAR
        ,seller_bc_type VARCHAR
        ,seller_tag  VARCHAR
        ,PRIMARY KEY (pk)
        ,PERIOD FOR SYSTEM_TIME 
    ) with (
        type = 'custom',
        tableFactoryClass = 'com.alibaba.blink.connectors.hologres.HologresTableFactory',
        `endpoint` = '',
        `userName` = '',
        `password` = '',
        `dbName` = 'hologres-test',
        `tableName` = 'test',
        `cache`='LRU',
        `cacheSize`='100000',
        `cacheTTLMs`='1800000',
        `asyncResultOrder`='unordered',
        `asyncTimeoutMs`='900000',
        `asyncCapacity`='1200',
        `async` = 'true'
    );
    ```


## 数据类型映射

实时计算数据类型与Hologres数据类型的映射关系请参见[数据类型汇总](/intl.zh-CN/SQL参考/数据类型/数据类型汇总.md)。

