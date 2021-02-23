---
keyword: [开源Flink, 实时导入数据]
---

# 开源Flink实时导入数据至Hologres Demo

本文以一个示例为您演示开源Flink如何实时写入数据至Hologres。

-   开通Hologres实例，并连接开发工具。本次示例使用psql客户端连接Hologres，详情请参见[psql快速入门](/cn.zh-CN/快速入门/psql快速入门.md)。
-   创建Flink集群。您可以进入Flink官网下载二进制文件，启动一个Standalone集群，详细操作步骤请参见[集群搭建](https://ci.apache.org/projects/flink/flink-docs-release-1.11/try-flink/local_installation.html)。本次示例使用Flink1.10集群。
-   请您根据实际业务准备Flink的输入数据源。

## 操作步骤

1.  Hologres创建结果表。

    Holoogres实例连接开发工具后，您需要创建一张结果表，用于接收实时写入的数据。示例语句如下。

    ```
    begin;
    create table order_details(user_id bigint, user_name text, item_id bigint, item_name text, price numeric(38, 2), province text, city text, ip text, longitude text, latitude text, sale_timestamp timestamptz not null, primary key(user_id, item_id));
    call set_table_property ('order_details', 'segment_key', 'sale_timestamp');
    commit;
    ```

2.  下载并编译Flink的JAR文件。

    1.  下载并安装Hologres Connector依赖的JAR文件[hologres-flink-connector-1.10-jar-with-dependencies.jar](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/171614/cn_zh/1614066580998/hologres-flink-connector-1.10-jar-with-dependencies.jar)，示例语句如下。

        ```
        mvn install:install-file -Dfile=hologres-flink-connector-1.10-jar-with-dependencies.jar -DgroupId=org.apache.flink -DartifactId=hologres-flink-connector -Dversion=1.10 -Dpackaging=jar -DgeneratePom=true
        ```

    2.  进入Hologres的[Git官方示例库](https://github.com/hologres/hologres-flink-examples)，下载并编译JAR文件，示例语句如下。

        ```
        git clone https://github.com/hologres/hologres-flink-examples.git
        cd hologres-flink-examples
        git checkout -b example
        mvn package -DskipTests
        ```

3.  提交Flink作业。

    编译完JAR文件后，配置作业参数，提交Flink作业，示例语句如下。

    **说明：** 示例使用命令行方式提交Flink作业，您也可以选择使用Flink Web页面提交作业。

    ```
    flink run -c io.hologres.flink.example.HologresSinkExample ../hologres-flink-example/target/hologres-flink-examples-1.0.0-jar-with-dependencies.jar --endpoint $ENDPOINT --username $USERNAME --password $PASSWORD --database $DATABASE --tablename order_details
    ```

    参数说明如下表所示。

    |参数|描述|示例|
    |--|--|--|
    |endpoint|Hologres的Endpoint地址。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取Endpoint。

**说明：** 本地Flink请使用Hologres的公共网络地址，阿里云VPC网络请使用Hologres的VPC网络地址。

|`ssseeee-cn-hangzhou.hologres.aliyuncs.com:80`|
    |username|当前阿里云账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。

|无|
    |password|当前阿里云账号的AccessKey Secret。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。

|无|
    |database|连接的Hologres数据库名称。|`hologres_demo`|
    |tablename|Hologres接收数据的表名称。|`order_details`|

4.  Hologres查询数据。

    成功启动任务后，您可以在Hologres中实时查询写入的数据。示例语句如下。

    ```
    select count(1) from order_details;
    select item_id, sum(price) as total from order_details group by item_id limit 10;
    ```


