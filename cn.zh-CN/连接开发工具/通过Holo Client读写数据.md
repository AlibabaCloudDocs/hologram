---
keyword: [Holo Client, Hologres]
---

# 通过Holo Client读写数据

为了更高效的支持大批量数据的写入（批量、实时同步至Hologres），以及支持高QPS的点查（维表关联）场景，Hologres在JDBC的基础上自研了一款开发接口Holo Client。您可以通过Holo Client实现高性能的批量、实时同步以及高QPS的点查询，自动攒批，提高吞吐。本文将为您介绍Holo Client的使用。

## 注意事项

在Hologres中使用Holo Client需要注意如下事项：

-   仅Hologres V0.9及以上版本支持通过Holo Client读写数据，如果您的实例是V0.9以下版本，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)或加入在线支持钉钉群申请升级实例。
-   Holo Client基于JDBC实现，使用时您可以使用如下方式确认实例剩余可用连接数。
    -   查看最大连接数。

        ```
        show max_connections;
        ```

    -   查看已使用连接数。

        ```
        select count(*) from pg_stat_activity where backend_type='client backend';
        ```


## 引入Holo Client

Holo Client依赖特殊的Postgres JDBC版本，再使用之前您需要确认项目中并未依赖org.postgresql:postgresql，避免出现冲突。引入方法如下：

-   Maven

    ```
    <dependency>
      <groupId>com.alibaba.hologres</groupId>
      <artifactId>holo-client</artifactId>
      <version>1.2.13.5</version>
    </dependency>
    ```

-   Gradle

    ```
    implementation 'com.alibaba.hologres:holo-client:1.2.13.5'
    ```


## Holo Client已知版本限制

-   Holo Client 1.2.8版本，引入`INSERT_OR_IGNORE`和`INSERT_OR_UPDATE`模式下，INSERT和DELETE不保证排序的问题。 当前问题在1.2.10.3版本完成修复。
-   Holo Client 1.2.6版本，引入`GetBuilder.withSelectedColumns`不生效，每次仍会获取所有列的问题。 当前问题在1.2.12.1版本完成修复。
-   Holo Client 1.2.9.1版本，引入Scan如果设置了`withSelectedColumn`无法查询的问题。 当前问题在1.2.12.1版本完成修复。
-   Holo Client 1.2.0版本，引入当主键包含bytea列时，GET请求无法返回结果，PUT请求无法保证排序的问题。 当前问题在1.2.12.1版本完成修复。
-   Holo Client 1.2.0版本，引入当PK的Hash为`Integer.MIN_VALUE`时将写入失败的问题。 当前问题在1.2.12.1版本完成修复。

## 连接数说明

-   Holo Client最多会同时启动Max\(writeThreadSize,readThreadSize\)个连接。
-   连接的idle时间超过connectionMaxIdleMs时该连接会被释放。
-   当实例剩余的连接数不足以处理当前请求量时，会自动创建新连接。

## 数据写入

建议项目中创建Holo Client单例，通过writeThreadSize和readThreadSize参数控制读写的并发。

**说明：** 每个并发占用1个JDBC连接，空闲的连接超过默认的connectionMaxIdleMs值将被自动回收。

-   **写入普通表**
    -   语法示例

        ```
        // 配置参数,url格式为 jdbc:postgresql://host:port/db
        HoloConfig config = new HoloConfig();
        config.setJdbcUrl(url);
        config.setUsername(username);
        config.setPassword(password);
        
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
-   **写入分区表**
    -   若分区已存在，不论DynamicPartition为何值，写入数据都将自动路由到正确的分区表中。
    -   若分区不存在，DynamicPartition设置为true时，将会自动创建不存在的分区，否则抛出异常。
    -   写入分区表在Hologres V0.9及以上版本正式发布并有才能获得较好的性能，如果为V0.8版本想要实现较好的写入性能，建议先写到临时表，再通过`insert into xxx select ...`的方式写入到分区表。
        -   语法示例

            ```
            // 配置参数,url格式为 jdbc:postgresql://host:port/db
            HoloConfig config = new HoloConfig();
            config.setJdbcUrl(url);
            config.setUsername(username);
            config.setPassword(password);
            
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
-   **写入含主键表**
    -   语法示例

        ```
        // 配置参数,url格式为 jdbc:postgresql://host:port/db
        HoloConfig config = new HoloConfig();
        config.setJdbcUrl(url);
        config.setUsername(username);
        config.setPassword(password);
        
        config.setWriteMode(WriteMode.INSERT_OR_REPLACE);//配置主键冲突时策略
        
        try (HoloClient client = new HoloClient(config)) {
            //create table t0(id int not null,name0 text,address text,primary key(id))
            TableSchema schema0 = client.getTableSchema("t0");
            Put put = new Put(schema0);
            put.setObject("id", 1);
            put.setObject("name0", "name0");
            put.setObject("address", "address0");
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
-   **基于主键删除**
    -   语法示例

        ```
        // 配置参数,url格式为 jdbc:postgresql://host:port/db
        HoloConfig config = new HoloConfig();
        config.setJdbcUrl(url);
        config.setUsername(username);
        config.setPassword(password);
        
        config.setWriteMode(WriteMode.INSERT_OR_REPLACE);//配置主键冲突时策略
        
        try (HoloClient client = new HoloClient(config)) {
            //create table t0(id int not null,name0 text,address text,primary key(id))
            TableSchema schema0 = client.getTableSchema("t0");
            Put put = new Put(schema0);
            put.getRecord().setType(SqlCommandType.DELETE);
            put.setObject("id", 1);
            client.put(put); 
            ...
            client.flush();//强制提交所有未提交put请求；HoloClient内部也会根据WriteBatchSize、WriteBatchByteSize、writeMaxIntervalMs三个参数自动提交
        catch(HoloClientException e){
        }
        ```

    -   参数说明，请参见[HoloConfig参数说明](#section_azm_quv_jps)。

## 数据查询

Holo Client目前支持基于完整主键的查询和Scan查询。

-   **基于完整主键查询**
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
            
            Get get = Get.newBuilder(schema).setPrimaryKey("id", 0).build(); // where id=1;
            client.get(get).thenAcceptAsync((record)->{
                // do something after get result
            });
        catch(HoloClientException e){
        }
        ```

    -   参数说明，请参见[HoloConfig参数说明](#section_azm_quv_jps)。
-   **Scan查询**
    -   语法示例

        ```
        // 配置参数,url格式为 jdbc:postgresql://host:port/db
        HoloConfig config = new HoloConfig();
        config.setJdbcUrl(url);
        config.setUsername(username);
        config.setPassword(password);
        try (HoloClient client = new HoloClient(config)) {
            //create table t0 (id int not null,name text not null,address text,primary key(id,name))
            TableSchema schema0 = client.getTableSchema("t0");
            
            Scan scan = Scan.newBuilder(schema).addEqualFilter("id", 102).addRangeFilter("name", "3", "4").withSelectedColumn("address").build();
            //等同于select address from t0 where id=102 and name>=3 and name<4 order by id;
            int size = 0;
            try (RecordScanner rs = client.scan(scan)) {
                while (rs.next()) {
                    Record record = rs.getRecord();
                    //handle record
                }
            }
            //不排序
            scan = Scan.newBuilder(schema).addEqualFilter("id", 102).addRangeFilter("name", "3", "4").withSelectedColumn("address").setSortKeys(SortKeys.NONE).build();
            //等同于select address from t0 where id=102 and name>=3 and name<4; 
            size = 0;
            try (RecordScanner rs = client.scan(scan)) {
                while (rs.next()) {
                    Record record = rs.getRecord();
                    //handle record
                }
            }
        catch(HoloClientException e){
        }   
        ```

    -   参数说明，请参见[HoloConfig参数说明](#section_azm_quv_jps)。

## 自定义操作

-   语法示例

    ```
    HoloConfig config = new HoloConfig();
    config.setJdbcUrl(url);
    config.setUsername(username);
    config.setPassword(password);
    try (HoloClient client = new HoloClient(config)) {
        client.sql(conn -> {
                    try (Statement stat = conn.createStatement()) {
                        stat.execute("create table t0(id int)");
                    }
                    return null;
                }).get();
    catch(HoloClientException e){
    }
    ```

-   参数说明，请参见[HoloConfig参数说明](#section_azm_quv_jps)。

## 异常处理

对put和flush捕捉HoloClientWithDetailsException异常，异常中包含写失败的记录列表，具体内容如下：

```
public void doPut(HoloClient client, Put put) throws HoloClientException {
    try{
        client.put(put);
    }catch(HoloClientWithDetailsException e){
        for(int i=0;i<e.size();++i){
            //写入失败的记录
            Record failedRecord = e.getFailRecord(i);
            //写入失败的原因
            HoloClientException cause = e.getException(i);
            //脏数据处理逻辑
        }
    }catch(HoloClientException e){
        //非HoloClientWithDetailsException的异常一般是fatal的
        throw e;
    }
}

public void doFlush(HoloClient client) throws HoloClientException {
    try{
        client.flush();
    }catch(HoloClientWithDetailsException e){
        for(int i=0;i<e.size();++i){
            //写入失败的记录
            Record failedRecord = e.getFailRecord(i);
            //写入失败的原因
            HoloClientException cause = e.getException(i);
            //脏数据处理逻辑
        }
    }catch(HoloClientException e){
        //非HoloClientWithDetailsException的异常一般是fatal的
        throw e;
    }
}
```

## HoloConfig参数说明

示例代码中均使用到HoloConfig参数，具体使用和说明请参见如下表格。

-   **基础配置**

    |参数|是否必填|说明|引入版本|
    |--|----|--|----|
    |jdbcUrl|是|表达格式为`jdbc:postgresql://host:port/db`其具体参数说明如下：    -   `host:port`是Hologres的endpoint。
    -   `db`是Hologres的数据库名。
您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取。|1.2.3|
    |username|是|当前账号的Access ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)获取。|1.2.3|
    |password|是|当前账号的Access Key。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)获取。|1.2.3|
    |appName|否|jdbc的applicationName参数。默认值为`holo-client`。|1.2.9.1|

-   **写入配置**

    |参数|默认值|说明|引入版本|
    |--|---|--|----|
    |dynamicPartition|false|当分区不存在时是否自动创建分区。取值如下：    -   true，当分区不存在时自动创建分区。
    -   false，当分区不存在时不自动创建分区。
|1.2.3|
    |writeMode|INSERT\_OR\_REPLACE|当INSERT目标表为有主键的表时采用不同策略：    -   LINSERT\_OR\_IGNORE，当主键冲突时，不写入。
    -   INSERT\_OR\_UPDATE，当主键冲突时，更新相应列。
    -   INSERT\_OR\_REPLACE，当主键冲突时，更新所有列。
|1.2.3|
    |writeBatchSize|512|每个写入线程的最大批次大小，在经过WriteMode合并后的Put数量达到writeBatchSize时进行一次批量提交。|1.2.3|
    |writeBatchByteSize|2MB|每个写入现成的最大批次bytes大小，在经过WriteMode合并后的Put数据字节数达到writeBatchByteSize时进行一次批量提交。|1.2.3|
    |writeBatchTotalByteSize|20MB|所有表最大批次bytes大小，在经过WriteMode合并后的Put数据字节数达到writeBatchByteSize时进行一次批量提交。|1.2.8.1|
    |writeMaxIntervalMs|10000 ms|距离上次提交超过writeMaxIntervalMs会触发一次批量提交。|1.2.4|
    |writeFailStrategy|TYR\_ONE\_BY\_ONE|当某一批次提交失败时，会将批次内的记录逐条提交（保序），单条提交失败的记录将会跟随异常HoloClientWithDatailsException被抛出。|1.2.4|
    |writerShardCountResizeIntervalMs|30s|主动调用flush时，触发resize，两次resize间隔不短于writerShardCountResizeIntervalMs。|1.2.10.1|
    |flushMaxWaitMs|60000ms|flush操作的最长等待时间。|1.2.5|
    |inputNumberAsEpochMsForDatetimeColumn|false|当Number写入Date/timestamp/timestamptz列时，若为true，将number视作ApochMs。|1.2.5|
    |inputStringAsEpochMsForDatetimeColumn|false|当String写入Date/timestamp/timestamptz列时，若为true，将String视作ApochMs。|1.2.6|
    |removeU0000InTextColumnValue|true|当写入Text/Varchar列时，若为true，剔除字符串中的`\u0000`|1.2.10.1|
    |enableDefaultForNotNullColumn|true|当取值为true时，当表为not null属性且未指定default属性时，当字段传入null时，将以默认值写入。    -   String默认为“”
    -   Number默认为0
    -   Date/timestamp/timestamptz默认为1970-01-01 00:00:00
|1.2.6|
    |defaultTimeStampText|null|当enableDefaultForNotNullColumn=true时，Date/timestamp/timestamptz的默认值。|1.2.6|
    |reWriteBatchedDeletes|true|当reWriteBatchedDeletes=true时，会将多条DELETE请求合并为一条SQL语句提升性能。|1.2.12.1|

-   **查询配置**

    |参数|默认值|说明|引入版本|
    |--|---|--|----|
    |readThreadSize|1|点查并发线程数（每个并发占用1个数据库连接）。|1.2.4|
    |readBatchSize|128|点查最大批次大小。|1.2.3|
    |readBatchQueueSize|256|点查请求缓冲队列大小。|1.2.4|
    |scanFetchSize|256|Scan操作一次fetch的行数。|1.2.9.1|
    |scanTimeoutSeconds|256|Scan操作的超时时间。|1.2.9.1|

-   **连接配置**

    |参数|默认值|说明|引入版本|
    |--|---|--|----|
    |retryCount|3|当连接故障时，写入和查询的重试次数。|1.2.3|
    |retrySleepInitMs|1000ms|每次重试的等待时间=retrySleepInitMs+retry\*retrySleepStepMs。|1.2.3|
    |retrySleepStepMs|10\*1000ms|每次重试的等待时间=retrySleepInitMs+retry\*retrySleepStepMs。|1.2.3|
    |connectionMaxIdleMs|60000ms|写入线程和点查线程数据库连接的最大Idle时间，超过该默认值连接将被释放。|1.2.4|
    |metaCacheTTL|1min|getTableSchema信息的本地缓存时间。|1.2.6|
    |metaAutoRefreshFactor|4|当tableSchema cache剩余存活时间短于 metaCacheTTL/metaAutoRefreshFactor 将自动刷新cache。|1.2.10.1|


