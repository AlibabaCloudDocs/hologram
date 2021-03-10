---
keyword: [Holo Client, Hologres]
---

# Read and write data by using Holo Client

This topic describes how to use Holo Client.

## Background information

Holo Client is developed by the Hologres team based on Java Database Connectivity \(JDBC\). Holo Client allows you to write a large amount of data at a time and perform point queries that support high QPS. For example, you can use Holo Client to synchronize a large amount of data to Hologres in real time or query data in a table that is associated with a dimension table. By using Holo Client, you can read and write data in batches to improve the performance and read/write throughput.

## Notes

Take note of the following rules when you use Holo Client in Hologres:

-   You can use Holo Client to read and write data only when you use an instance of Hologres V0.9 or later. To upgrade an instance, [submit a ticket](https://workorder-intl.console.aliyun.com/) or join the Hologres DingTalk group for technical support.
-   Holo Client works based on JDBC. Before you use Holo Client, you must check the remaining quota of connections for your Hologres instance.
    -   Run the following command to query the maximum number of connections allowed for your instance:

        ```
        show max_connections;
        ```

    -   Run the following command to query the number of connections that you have created for your instance:

        ```
        select count(*) from pg_stat_activity;
        ```


## Connect Hologres to Holo Client

Holo Client depends on a special version of the PostgreSQL JDBC driver. Before you connect Hologres to Holo Client, make sure that the org.postgresql:postgresql dependency is not added to your project. This prevents conflicts. You can use one of the following methods to connect Hologres to Holo Client:

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


## Write data

We recommend that you use Holo Client in a singleton pattern and manage the concurrency of read and write requests by using the writeThreadSize and readThreadSize parameters.

**Note:** Each parallel task occupies one JDBC connection. The connectionMaxIdleMs parameter specifies an idle timeout period that applies to connections. If a connection does not send or receive data by the time that the idle timeout period elapses, the connection is automatically recycled.

-   Write data to a standard table
    -   Sample code

        ```
        // Set the parameters. Specify the JDBC URL in the format of jdbc:postgresql://host:port/db. Replace host:port with an endpoint of the destination Hologres instance and db with the name of the destination Hologres database.
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
            client.flush(); // Forcibly submit all the PUT requests that have not been submitted. Holo Client submits requests based on the values of the writeBatchSize, writeBatchByteSize, and writeMaxIntervalMs parameters.
        catch(HoloClientException e){
        }
        ```

    -   For more information, see [Parameters in the HoloConfig object](#section_azm_quv_jps).
-   Write data to a partitioned table
    -   If partitions exist, Holo Client routes data to the corresponding partitions in the partitioned table, regardless of the value of the DynamicPartition parameter.
    -   If partitions do not exist and the DynamicPartition parameter is set to true, Holo Client creates the required partitions. If partitions do not exist and the DynamicPartition parameter is set to false, an error is returned.
    -   We recommend that you use Hologres V0.9 or later to write data to a partitioned table and publish the data to ensure favorable performance. Instances of Hologres V0.8 support only canary release. To ensure favorable write performance by using an instance of Hologres V0.8, we recommend that you write data to a temporary table, and then execute the INSERT INTO SELECT statement to write the data to the partitioned table.
        -   Sample code

            ```
            // Set the parameters. Specify the JDBC URL in the format of jdbc:postgresql://host:port/db. Replace host:port with an endpoint of the destination Hologres instance and db with the name of the destination Hologres database.
            HoloConfig config = new HoloConfig();
            config.setJdbcUrl(url);
            config.setUsername(username);
            config.setPassword(password);
            
            config.setWriteBatchByteSize(2*1024*1024L);
            config.setWriteBatchSize(512);
            
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
-   Write data to a table that contains a primary key
    -   Sample code

        ```
        // Set the parameters. Specify the JDBC URL in the format of jdbc:postgresql://host:port/db. Replace host:port with an endpoint of the destination Hologres instance and db with the name of the destination Hologres database.
        HoloConfig config = new HoloConfig();
        config.setJdbcUrl(url);
        config.setUsername(username);
        config.setPassword(password);
        
        config.setWriteBatchByteSize(2*1024*1024L);
        config.setWriteBatchSize(512);
        
        config.setWriteMode(WriteMode.INSERT_OR_REPLACE); // Configure a policy that handles primary key conflicts.
        
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
            client.flush(); // Forcibly submit all the PUT requests that have not been submitted. Holo Client submits requests based on the values of the writeBatchSize, writeBatchByteSize, and writeMaxIntervalMs parameters.
        catch(HoloClientException e){
        }
        ```

    -   For more information, see [Parameters in the HoloConfig object](#section_azm_quv_jps).

## Read data

When you use Holo Client to read data, you must specify the primary key value.

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
        
        Object[] primaryKeys=new Object[]{1}; // where id=1;
        client.get(new Get(schema0,primaryKeys)).thenAcceptAsync((record)->{
            // do something after get result
        });
    catch(HoloClientException e){
    }
    ```

-   For more information, see [Parameters in the HoloConfig object](#section_azm_quv_jps).

## Parameters in the HoloConfig object

The sample code in both the [Write data](#section_7nm_jf1_jze) and [Read data](#section_c95_eu0_nau) sections involves parameters in the HoloConfig object. The following table describes the parameters.

|Parameter|Default value|Description|
|---------|-------------|-----------|
|jdbcUrl|jdbc:postgresql://host:port/db|The URL of the JDBC connection. You must replace host:port with an endpoint of the destination Hologres instance and db with the name of the destination Hologres database. You can view the information on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
|username|The AccessKey ID of the current Alibaba Cloud account.|You can obtain the AccessKey ID from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.|
|password|The AccessKey secret of the current Alibaba Cloud account.|You can obtain the AccessKey secret from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.|
|dynamicPartition|false|Specifies whether to automatically create partitions. When this parameter is set to true, the required partitions are automatically created if they do not exist.|
|writeMode|INSERT\_OR\_REPLACE|The policy that is used to handle primary key conflicts. This parameter applies when destination tables contain primary keys. Valid values:-   LINSERT\_OR\_IGNORE: discards the data to be written if a conflict occurs.
-   INSERT\_OR\_UPDATE: updates the corresponding columns of the destination table if a conflict occurs.
-   INSERT\_OR\_REPLACE: updates all the columns of the destination table if a conflict occurs. |
|writeBatchSize|512|The maximum number of requests allowed in a batch in a thread to write data. If the total number of the PUT requests reaches the value of the writeBatchSize parameter after conflicts are handled based on the writeMode parameter, the data is submitted in a batch.|
|writeBufferSize|640|The maximum number of queued requests allowed in a thread to write data.|
|writeBatchByteSize|2\*1024\*1024|The maximum number of bytes allowed in a batch in a thread to write data. If the total number of bytes of the PUT requests reaches the value of the writeBatchByteSize parameter after conflicts are handled based on the writeMode parameter, the data is submitted in a batch.|
|writeMaxIntervalMs|10000 ms|The intervals at which data is submitted in a batch.|
|writeFailStrategy|TYR\_ONE\_BY\_ONE|The policy that is used to handle submission failures. If a batch fails to be submitted, Holo Client submits the data records in the batch in the specified sequence. If a single data record fails to be submitted, Holo Client returns a HoloClientWithDatailsException error message that contains information about the data record.|
|writeThreadSize|1|The number of concurrent threads to write data. Each thread occupies one connection.|
|inputNumberAsEpochMsForDatetimeColumn|false|Specifies whether to convert an input number as a timestamp that indicates the number of milliseconds that have elapsed since 00:00:00 Thursday, 1 January 1970 if the number is written to a column of the DATE, TIMESTAMP, or TIMESTAMPTZ type.|
|flushMaxWaitMs|60000 ms|The intervals at which all the PUT requests that have not been submitted are forcibly submitted.|
|readThreadSize|1|The number of concurrent threads to perform a point query. Each thread occupies one connection.|
|readBatchSize|128|The maximum number of requests allowed in a batch in a thread to perform a point query.|
|readBatchQueueSize|256|The maximum number of queued requests allowed in a thread to perform a point query.|
|retryCount|3|The maximum number of retries allowed to read and write data if a connection failure occurs.|
|retrySleepInitMs|1000 ms|The intervals at which retries are performed. The time consumed by the retries for a request is calculated in the following way: Value of the retrySleepInitMs parameter + Number of retries × Value of the retrySleepStepMs parameter.|
|retrySleepStepMs|10\*1000 ms|The time required for a retry. The time consumed by the retries for a request is calculated in the following way: Value of the retrySleepInitMs parameter + Number of retries × Value of the retrySleepStepMs parameter.|
|failFastWhenInit|true|Specifies whether to return an error message if a connection attempt fails when Holo Client initializes the connection. Holo Client uses the lazy initialization mechanism to initialize the connections that are used to read and write data.|
|connectionMaxIdleMs|60000 ms|The idle timeout period that applies to connections that are used to read and write data. If a connection does not send or receive data by the time that the idle timeout period elapses, Holo Client automatically releases the connection.|

