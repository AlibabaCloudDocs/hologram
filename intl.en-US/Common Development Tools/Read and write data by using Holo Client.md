---
keyword: [Holo Client, Hologres]
---

# Read and write data by using Holo Client

This topic describes how to use Holo Client.

## Background information

Holo Client is developed by the Hologres team based on Java Database Connectivity \(JDBC\). Holo Client allows you to write a large amount of data at a time and perform point queries that support high QPS. For example, you can use Holo Client to synchronize a large amount of data to Hologres in real time or query data in a table that is associated with a dimension table. Holo Client automatically collects data in batches to improve the read/write performance and throughput.

## Usage notes

Take note of the following rules when you use Holo Client in Hologres:

-   You can use Holo Client to read and write data only when you use an instance of Hologres V0.9 or later. To upgrade an instance, [submit a ticket](https://workorder-intl.console.aliyun.com/) or join the Hologres DingTalk group for technical support.
-   Holo Client works based on JDBC. Before you use Holo Client, you can check the remaining quota of connections for your Hologres instance by using the following methods:
    -   Run the following command to query the maximum number of connections allowed for your instance:

        ```
        show max_connections;
        ```

    -   Run the following command to query the number of connections that you have created for your instance:

        ```
        select count(*) from pg_stat_activity where backend_type='client backend';
        ```


## Connect Hologres to Holo Client

Holo Client depends on a special version of the PostgreSQL JDBC driver. Before you connect Hologres to Holo Client, make sure that the org.postgresql:postgresql dependency is not added to your project. Otherwise, conflicts may occur. You can use one of the following methods to connect Hologres to Holo Client:

-   Maven

    ```
    <dependency>
      <groupId>com.alibaba.hologres</groupId>
      <artifactId>holo-client</artifactId>
      <version>1.2.10.2</version>
    </dependency>
    ```

-   Gradle

    ```
    implementation 'com.alibaba.hologres:holo-client:1.2.10.2'
    ```


## Limits on connections

-   The number of connections that Holo Client establishes at a time cannot exceed the result of the Max\(writeThreadSize,readThreadSize\) function.
-   A connection is released if it does not send or receive data by the time the idle timeout period specified by the connectionMaxIdleMs parameter elapses.
-   If the remaining quota of connections for your Hologres instance is insufficient, Holo Client automatically creates connections to meet your business needs.

## Write data

We recommend that you use Holo Client in a singleton pattern and manage the concurrency of read and write requests by using the writeThreadSize and readThreadSize parameters.

**Note:** Each parallel task occupies one JDBC connection. The connectionMaxIdleMs parameter specifies an idle timeout period that applies to connections. If a connection does not send or receive data by the time the idle timeout period elapses, the connection is automatically recycled.

-   **Write data to a standard table**
    -   Sample code

        ```
        // Set the parameters. Specify the JDBC URL in the format of jdbc:postgresql://host:port/db. Replace host:port with an endpoint of the destination Hologres instance and db with the name of the destination Hologres database.
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
            client.flush(); // Forcibly submit all the PUT requests that have not been submitted. Holo Client submits requests based on the values of the writeBatchSize, writeBatchByteSize, and writeMaxIntervalMs parameters.
        catch(HoloClientException e){
        }
        ```

    -   For more information, see [Parameters in the HoloConfig object](#section_azm_quv_jps).
-   **Write data to a partitioned table**
    -   If partitions exist, Holo Client routes data to the corresponding partitions in the partitioned table, regardless of the value of the DynamicPartition parameter.
    -   If partitions do not exist and the DynamicPartition parameter is set to true, Holo Client creates the required partitions. If partitions do not exist and the DynamicPartition parameter is set to false, an error is returned.
    -   You can obtain favorable performance when you write data to a partitioned table in an instance of Hologres V0.9 or a later version that is officially released. Hologres V0.8 is released in canary release mode. If you want to write data to a partitioned table in an instance of Hologres V0.8, we recommend that you write the data to a temporary table, and then execute the INSERT INTO SELECT statement to write the data to the partitioned table. This ensures favorable write performance.
        -   Sample code

            ```
            // Set the parameters. Specify the JDBC URL in the format of jdbc:postgresql://host:port/db. Replace host:port with an endpoint of the destination Hologres instance and db with the name of the destination Hologres database.
            HoloConfig config = new HoloConfig();
            config.setJdbcUrl(url);
            config.setUsername(username);
            config.setPassword(password);
            
            config.setDynamicPartition(true); // Create partitions if the required partitions do not exist.
            
            try (HoloClient client = new HoloClient(config)) {
                //create table t0(id int not null,region text not null,name text,primary key(id,region)) partition by list(region)
                TableSchema schema0 = client.getTableSchema("t0");
                Put put = new Put(schema0);
                put.setObject("id", 1);
                put.setObject("region", "SH");
                put.setObject("name", "name0");
                client.put(put); 
                ...
                client.flush(); // Forcibly submit all the PUT requests that have not been submitted. Holo Client submits requests based on the values of the writeBatchSize, writeBatchByteSize, and writeMaxIntervalMs parameters.
            catch(HoloClientException e){
            }
            ```

        -   For more information, see [Parameters in the HoloConfig object](#section_azm_quv_jps).
-   **Write data to a table that contains a primary key**
    -   Sample code

        ```
        // Set the parameters. Specify the JDBC URL in the format of jdbc:postgresql://host:port/db. Replace host:port with an endpoint of the destination Hologres instance and db with the name of the destination Hologres database.
        HoloConfig config = new HoloConfig();
        config.setJdbcUrl(url);
        config.setUsername(username);
        config.setPassword(password);
        
        config.setWriteMode(WriteMode.INSERT_OR_REPLACE); // Configure a policy that handles primary key conflicts.
        
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
            client.flush(); // Forcibly submit all the PUT requests that have not been submitted. Holo Client submits requests based on the values of the writeBatchSize, writeBatchByteSize, and writeMaxIntervalMs parameters.
        catch(HoloClientException e){
        }
        ```

    -   For more information, see [Parameters in the HoloConfig object](#section_azm_quv_jps).
-   **Delete data based on a primary key**
    -   Sample code

        ```
        // Set the parameters. Specify the JDBC URL in the format of jdbc:postgresql://host:port/db. Replace host:port with an endpoint of the destination Hologres instance and db with the name of the destination Hologres database.
        HoloConfig config = new HoloConfig();
        config.setJdbcUrl(url);
        config.setUsername(username);
        config.setPassword(password);
        
        config.setWriteMode(WriteMode.INSERT_OR_REPLACE); // Configure a policy that handles primary key conflicts.
        
        try (HoloClient client = new HoloClient(config)) {
            //create table t0(id int not null,name0 text,address text,primary key(id))
            TableSchema schema0 = client.getTableSchema("t0");
            Put put = new Put(schema0);
            put.getRecord().setType(SqlCommandType.DELETE);
            put.setObject("id", 1);
            client.put(put); 
            ...
            client.flush(); // Forcibly submit all the PUT requests that have not been submitted. Holo Client submits requests based on the values of the writeBatchSize, writeBatchByteSize, and writeMaxIntervalMs parameters.
        catch(HoloClientException e){
        }
        ```

    -   For more information, see [Parameters in the HoloConfig object](#section_azm_quv_jps).

## Read data

Holo Client allows you to read data based on a complete primary key or by performing the SCAN operation.

-   **Read data based on a complete primary key**
    -   Sample code

        ```
        // Set the parameters. Specify the JDBC URL in the format of jdbc:postgresql://host:port/db. Replace host:port with an endpoint of the destination Hologres instance and db with the name of the destination Hologres database.
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

    -   For more information, see [Parameters in the HoloConfig object](#section_azm_quv_jps).
-   **Read data by performing the SCAN operation**
    -   Sample code

        ```
        // Set the parameters. Specify the JDBC URL in the format of jdbc:postgresql://host:port/db. Replace host:port with an endpoint of the destination Hologres instance and db with the name of the destination Hologres database.
        HoloConfig config = new HoloConfig();
        config.setJdbcUrl(url);
        config.setUsername(username);
        config.setPassword(password);
        try (HoloClient client = new HoloClient(config)) {
            //create table t0 (id int not null,name text not null,address text,primary key(id,name))
            TableSchema schema0 = client.getTableSchema("t0");
            
            Scan scan = Scan.newBuilder(schema).addEqualFilter("id", 102).addRangeFilter("name", "3", "4").withSelectedColumn("address").build();
            // This statement is equivalent to the select address from t0 where id=102 and name>=3 and name<4 statement. 
            int size = 0;
            try (RecordScanner rs = client.scan(scan)) {
                while (rs.next()) {
                    Record record = rs.getRecord();
                    //handle record
                }
            }
        catch(HoloClientException e){
        }   
        ```

    -   For more information, see [Parameters in the HoloConfig object](#section_azm_quv_jps).

## Customize operations

-   Sample code

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

-   For more information, see [Parameters in the HoloConfig object](#section_azm_quv_jps).

## Handle exceptions

You can capture HoloClientWithDetailsException exceptions for PUT and FLUSH requests. The exceptions contain the details of the entries that fail to be written. The following sample code provides an example on how to capture HoloClientWithDetailsException exceptions:

```
public void doPut(HoloClient client, Put put) throws HoloClientException {
    try{
        client.put(put);
    }catch(HoloClientWithDetailsException e){
        for(int i=0;i<e.size();++i){
            // Obtain the entries that fail to be written.
            Record failedRecord = e.getFailRecord(i);
            // Obtain the reason why the entries fail to be written.
            HoloClientException cause = e.getException(i);
            // Process dirty data.
        }
    }catch(HoloClientException e){
        // The exceptions except for HoloClientWithDetailsException exceptions are often fatal.
        throw e;
    }
}

public void doFlush(HoloClient client) throws HoloClientException {
    try{
        client.flush();
    }catch(HoloClientWithDetailsException e){
        for(int i=0;i<e.size();++i){
            // Obtain the entries that fail to be written.
            Record failedRecord = e.getFailRecord(i);
            // Obtain the reason why the entries fail to be written.
            HoloClientException cause = e.getException(i);
            // Process dirty data.
        }
    }catch(HoloClientException e){
        // The exceptions except for HoloClientWithDetailsException exceptions are often fatal.
        throw e;
    }
}
```

## Parameters in the HoloConfig object

The sample code in this topic involves the parameters in the HoloConfig object. The following table describes the parameters.

-   **Basic configurations**

    |Parameter|Required|Description|Version|
    |---------|--------|-----------|-------|
    |jdbcUrl|Yes|The URL of the JDBC connection. The URL is in the format of `jdbc:postgresql://host:port/db` and includes the following variables:    -   `host:port`: the endpoint of the destination Hologres instance.
    -   `db`: the name of the destination Hologres database.
You can view the required information from the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|1.2.3|
    |username|Yes|The AccessKey ID of your Alibaba Cloud account. You can obtain the AccessKey ID from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.|1.2.3|
    |password|Yes|The AccessKey secret of your Alibaba Cloud account. You can obtain the AccessKey secret from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.|1.2.3|
    |appName|No|The name of the application that uses the JBDC connection. Default value: `holo-client`.|1.2.9.1|

-   **Data writing configurations**

    |Parameter|Default value|Description|Version|
    |---------|-------------|-----------|-------|
    |dynamicPartition|false|Specifies whether to automatically create partitions if partitions do not exist. Valid values:    -   true: automatically creates the required partitions.
    -   false: does not automatically create the required partitions.
|1.2.3|
    |writeMode|INSERT\_OR\_REPLACE|The policy that is used to handle primary key conflicts. This parameter applies when destination tables contain primary keys. Valid values:    -   LINSERT\_OR\_IGNORE: discards the data to be written if a conflict occurs.
    -   INSERT\_OR\_UPDATE: updates the corresponding columns of the destination table if a conflict occurs.
    -   INSERT\_OR\_REPLACE: updates all the columns of the destination table if a conflict occurs.
|1.2.3|
    |writeBatchSize|512|The maximum number of requests allowed in a batch in a thread to write data. If the total number of the PUT requests reaches the value of the writeBatchSize parameter after conflicts are handled based on the writeMode parameter, the data is submitted in a batch.|1.2.3|
    |writeBatchByteSize|2MB|The maximum number of bytes allowed in a batch in a thread to write data. If the total number of bytes of the PUT requests reaches the value of the writeBatchByteSize parameter after conflicts are handled based on the writeMode parameter, the data is submitted in a batch.|1.2.3|
    |writeBatchTotalByteSize|20MB|The maximum number of bytes allowed in a batch for all tables. If the total number of bytes of the PUT requests reaches the value of the writeBatchByteSize parameter after conflicts are handled based on the writeMode parameter, the data is submitted in a batch.|1.2.8.1|
    |writeMaxIntervalMs|10000 ms|The intervals at which data is submitted in a batch.|1.2.4|
    |writeFailStrategy|TYR\_ONE\_BY\_ONE|The policy that is used to handle submission failures. If a batch fails to be submitted, Holo Client submits the data entries in the batch in the specified sequence. If a data entry fails to be submitted, Holo Client throws a HoloClientWithDatailsException exception that contains detailed information about the data entry.|1.2.4|
    |writerShardCountResizeIntervalMs|30s|The minimum interval between the RESIZE operations that are triggered when you call the flush\(\) method.|1.2.10.1|
    |flushMaxWaitMs|60000ms|The maximum period of time to wait for a FLUSH operation to complete. The FLUSH operation is performed to forcibly submit all the PUT requests that have not been submitted.|1.2.5|
    |inputNumberAsEpochMsForDatetimeColumn|false|Specifies whether to convert an input number as a timestamp that indicates the number of milliseconds that have elapsed since 00:00:00 Thursday, 1 January 1970 if the number is written to a column of the DATE, TIMESTAMP, or TIMESTAMPTZ type.|1.2.5|
    |inputStringAsEpochMsForDatetimeColumn|false|Specifies whether to convert an input string as a timestamp that indicates the number of milliseconds that have elapsed since 00:00:00 Thursday, 1 January 1970 if the string is written to a column of the DATE, TIMESTAMP, or TIMESTAMPTZ type.|1.2.6|
    |removeU0000InTextColumnValue|true|Specifies whether to remove `\u0000` from a string if the string is written to a column of the TEXT or VARCHAR type.|1.2.10.1|
    |enableDefaultForNotNullColumn|true|Specifies whether to convert the null value to the specified default value when the null value is written to a column that adopts the NOT NULL constraint and has no default value specified.     -   If the destination column is of the STRING type, the null value is converted to an empty string \(""\).
    -   If the destination column is of the NUMBER type, the null value is converted to 0.
    -   If the destination column is of the DATE, TIMESTAMP, or TIMESTAMPTZ type, the null value is converted to 1970-01-01 00:00:00.
|1.2.6|
    |defaultTimeStampText|null|The default value that is used to replace the null value to be written to a column of the DATE, TIMESTAMP, or TIMESTAMPTZ type. This parameter takes effect when the enableDefaultForNotNullColumn parameter is set to true.|1.2.6|

-   **Data reading configurations**

    |Parameter|Default value|Description|Version|
    |---------|-------------|-----------|-------|
    |readThreadSize|1|The number of concurrent threads to perform a point query. Each thread occupies one connection.|1.2.4|
    |readBatchSize|128|The maximum number of requests allowed in a batch in a thread to perform a point query.|1.2.3|
    |readBatchQueueSize|256|The maximum number of queued requests allowed in a thread to perform a point query.|1.2.4|
    |scanFetchSize|256|The number of rows of data fetched by performing a SCAN operation.|1.2.9.1|
    |scanTimeoutSeconds|256|The maximum period of time to wait for a SCAN operation to complete.|1.2.9.1|

-   **Connection configurations**

    |Parameter|Default value|Description|Version|
    |---------|-------------|-----------|-------|
    |retryCount|3|The maximum number of retries allowed to read and write data if a connection failure occurs.|1.2.3|
    |retrySleepInitMs|1000ms|The time required for the initialization. The time consumed by the retries for a request is calculated in the following way: Value of the retrySleepInitMs parameter + Number of retries × Value of the retrySleepStepMs parameter.|1.2.3|
    |retrySleepStepMs|10\*1000ms|The time required for a retry. The time consumed by the retries for a request is calculated in the following way: Value of the retrySleepInitMs parameter + Number of retries × Value of the retrySleepStepMs parameter.|1.2.3|
    |connectionMaxIdleMs|60000ms|The idle timeout period that applies to the connections that are used to read and write data. If a connection does not send or receive data by the time the idle timeout period elapses, Holo Client automatically releases the connection.|1.2.4|
    |metaCacheTTL|1min|The time-to-live \(TTL\) of the table schema information in the cache.|1.2.6|
    |metaAutoRefreshFactor|4|The factor that controls the timing to automatically refresh the cache. If the remaining TTL of the table schema information in the cache is less than the result of dividing the value of the metaCacheTTL parameter by the value of the metaAutoRefreshFactor parameter, Holo Client automatically refreshes the cache.|1.2.10.1|


