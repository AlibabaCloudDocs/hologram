---
keyword: [Holo Client, Hologres]
---

# 通过Holo Client读写数据

本文将会为您介绍Holo Client的使用。

## 背景信息

为了更高效的支持大批量数据的写入（批量、实时同步至Hologres），以及支持高QPS的点查（维表关联）场景，Hologres在JDBC的基础上自研了一款开发接口Holo Client。您可以通过Holo Client实现高性能的批量、实时同步以及高QPS的点查询，自动攒批，提高吞吐。

## 注意事项

在Hologres中使用Holo Client需要注意如下事项：

-   仅Hologres V0.9及以上版本支持通过Holo Client读写数据，如果您的实例是V0.9以下版本，请您[提交工单](https://workorder-intl.console.aliyun.com/)或加入在线支持钉钉群申请升级实例。
-   Holo Client基于JDBC实现，使用时请确认实例剩余可用连接数。
    -   查看最大连接数

        ```
        show max_connections;
        ```

    -   查看已使用连接数

        ```
        select count(*) from pg_stat_activity;
        ```


## 引入Holo Client

Holo Client依赖特殊的Postgres JDBC版本，再使用之前您需要确认项目中并未依赖org.postgresql:postgresql，避免出现冲突。引入方法如下：

-   Maven

    ```
    <dependency>
      <groupId>com.alibaba.hologres</groupId>
      <artifactId>holo-client</artifactId>
      <version>1.2.5</version>
    </dependency>
    ```

-   Gradle

    ```
    implementation 'com.alibaba.hologres:holo-client:1.2.5'
    ```


## 数据写入

建议项目中创建Holo Client单例，通过writeThreadSize和readThreadSize参数控制读写的并发。

**说明：** 每个并发占用1个JDBC连接，空闲的连接超过默认的connectionMaxIdleMs值将被自动回收。

-   写入普通表
    -   语法示例

        ```
        // 配置参数,url格式为 jdbc:postgresql://host:port/db
        HoloConfig config = new HoloConfig();
        config.setJdbcUrl(url);
        config.setUsername(username);
        config.setPassword(password);
        
        config.setWriteBatchByteSize(2*1024*1024L);
        config.setWriteBatchSize(512);
        try (HoloClient client = new HoloClient(config)) {
            TableSchema schema0 = client.getTableSchema("t0");
            Put put = new Put(schema0);
            put.setObject("id", 1);
            put.setObject("name", "name0");
            put.setObject("address", "address0");
            client.put(put); 
            ...
            client.flush(); //强制提交所有未提交put请求；HoloClient内部也会根据WriteBatchSize、WriteBatchByteSize、writeMaxIntervalMs三个参数自动提交
        catch(HoloClientException e){
        }
        ```

    -   参数说明，请参见[HoloConfig参数说明](#section_azm_quv_jps)。
-   写入分区表
    -   若分区已存在，不论DynamicPartition为何值，写入数据都将自动路由到正确的分区表中。
    -   若分区不存在，DynamicPartition设置为true时，将会自动创建不存在的分区，否则抛出异常。
    -   写入分区表在Hologres V0.9及以上版本正式发布并有才能获得较好的性能，V0.8版本的实例为灰度测试，若是0.8版本想要实现较好的写入性能，建议先写到临时表，再通过insert into xxx select ...的方式写入到分区表。
        -   语法示例

            ```
            // 配置参数,url格式为 jdbc:postgresql://host:port/db
            HoloConfig config = new HoloConfig();
            config.setJdbcUrl(url);
            config.setUsername(username);
            config.setPassword(password);
            
            config.setWriteBatchByteSize(2*1024*1024L);
            config.setWriteBatchSize(512);
            
            config.setDynamicPartition(true); //当分区不存在时，将自动创建分区
            
            try (HoloClient client = new HoloClient(config)) {
                //create table t0(id int not null,region text not null,name text,primary key(id,region)) partition by list(region)
                TableSchema schema0 = client.getTableSchema("t0");
                Put put = new Put(schema0);
                put.setObject("id", 1);
                put.setObject("region", "SH");
                put.setObject("name", "name0");
                client.put(put); 
                ...
                client.flush(); //强制提交所有未提交put请求；HoloClient内部也会根据WriteBatchSize、WriteBatchByteSize、writeMaxIntervalMs三个参数自动提交
            catch(HoloClientException e){
            }
            ```

        -   参数说明，请参见[HoloConfig参数说明](#section_azm_quv_jps)。
-   写入含主键表
    -   语法示例

        ```
        // 配置参数,url格式为 jdbc:postgresql://host:port/db
        HoloConfig config = new HoloConfig();
        config.setJdbcUrl(url);
        config.setUsername(username);
        config.setPassword(password);
        
        config.setWriteBatchByteSize(2*1024*1024L);
        config.setWriteBatchSize(512);
        
        config.setWriteMode(WriteMode.INSERT_OR_REPLACE);//配置主键冲突时策略
        
        try (HoloClient client = new HoloClient(config)) {
            //create table t0(id int not null,name0 text,address text,primary key(id))
            TableSchema schema0 = client.getTableSchema("t0");
            Put put = new Put(schema0);
            put.setObject("id", 1);
            put.setObject("name0", "name0");
            put.setObject(address, "address0");
            client.put(put); 
            ...
            put = new Put(schema0);
            put.setObject(0, 1);
            put.setObject(1, "newName");
            put.setObject(2, "newAddress");
            client.put(put);
            ...
            client.flush();//强制提交所有未提交put请求；HoloClient内部也会根据WriteBatchSize、WriteBatchByteSize、writeMaxIntervalMs三个参数自动提交
        catch(HoloClientException e){
        }
        ```

    -   参数说明，请参见[HoloConfig参数说明](#section_azm_quv_jps)。

## 数据查询

Holo Client目前仅支持基于全主键的查询。

-   语法示例

    ```
    // 配置参数,url格式为 jdbc:postgresql://host:port/db
    HoloConfig config = new HoloConfig();
    config.setJdbcUrl(url);
    config.setUsername(username);
    config.setPassword(password);
    try (HoloClient client = new HoloClient(config)) {
        //create table t0(id int not null,name0 text,address text,primary key(id))
        TableSchema schema0 = client.getTableSchema("t0");
        
        Object[] primaryKeys=new Object[]{1}; // where id=1;
        client.get(new Get(schema0,primaryKeys)).thenAcceptAsync((record)->{
            // do something after get result
        });
    catch(HoloClientException e){
    }
    ```

-   参数说明，请参见[HoloConfig参数说明](#section_azm_quv_jps)。

## HoloConfig参数说明

[数据写入](#section_7nm_jf1_jze)和[数据查询](#section_c95_eu0_nau)的示例代码中均使用到HoloConfig参数，具体使用和说明请参见如下表格。

|参数|默认值|说明|
|--|---|--|
|jdbcUrl|jdbc:postgresql://host:port/db|其中host:port是Hologres的endpoint，db是Hologres的数据库名，您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取。|
|username|当前账号的Access ID。|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)获取。|
|password|当前账号的Access Key。|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)获取。|
|dynamicPartition|false|若为true，当分区不存在时自动创建分区。|
|writeMode|INSERT\_OR\_REPLACE|当INSERT目标表为有主键的表时采用不同策略：-   LINSERT\_OR\_IGNORE，当主键冲突时，不写入。
-   INSERT\_OR\_UPDATE，当主键冲突时，更新相应列。
-   INSERT\_OR\_REPLACE，当主键冲突时，更新所有列。 |
|writeBatchSize|512|每个写入线程的最大批次大小，在经过WriteMode合并后的Put数量达到writeBatchSize时进行一次批量提交。|
|writeBufferSize|640|每个写入线程的请求缓冲队列大小。|
|writeBatchByteSize|2\*1024\*1024|每个写入现成的最大批次bytes大小，在经过WriteMode合并后的Put数据字节数达到writeBatchByteSize时进行一次批量提交。|
|writeMaxIntervalMs|10000 ms|距离上次提交超过writeMaxIntervalMs会触发一次批量提交。|
|writeFailStrategy|TYR\_ONE\_BY\_ONE|当某一批次提交失败时，会将批次内的记录逐条提交（保序），单条提交失败的记录将会跟随异常HoloClientWithDatailsException被抛出。|
|writeThreadSize|1|写入并发线程数（每个并发占用1个数据库连接）。|
|inputNumberAsEpochMsForDatetimeColumn|false|当Number写入Date/timestamp/timestamptz列时，若为true，将number视作ApochMs。|
|flushMaxWaitMs|60000 ms|flush操作的最长等待时间。|
|readThreadSize|1|点查并发线程数（每个并发占用1个数据库连接）。|
|readBatchSize|128|点查最大批次大小。|
|readBatchQueueSize|256|点查请求缓冲队列大小。|
|retryCount|3|当连接故障时，写入和查询的重试次数。|
|retrySleepInitMs|1000 ms|每次重试的等待时间=retrySleepInitMs+retry\*retrySleepStepMs。|
|retrySleepStepMs|10\*1000 ms|每次重试的等待时间=retrySleepInitMs+retry\*retrySleepStepMs。|
|failFastWhenInit|true|写入和点查的数据库链接都是lazy创建的，failFastWhenInit为true时会在Holo Client实例化的时候就进行一次数据库的连接尝试，如果连接失败即刻抛异常。|
|connectionMaxIdleMs|60000 ms|写入线程和点查线程数据库连接的最大Idle时间，超过该默认值连接将被释放。|

