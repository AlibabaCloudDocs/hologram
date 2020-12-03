---
keyword: [best practice, optimize the performance of querying internal tables, Hologres]
---

# Optimize the performance of querying internal tables

This topic describes how to optimize the performance of querying internal tables in Hologres.

## View an execution plan

You can use the explain keyword and SQL query statements to view a distributed physical execution plan. An execution plan consists of a series of operators:

-   Data scanning operators: Seq Scan, Table Scan, and Index Scan.
-   Join operators: Hash Join and Nested Loop.
-   Aggregate operators: Hash Aggregate and Streaming Aggregate.
-   Distributed operators: Redistribute Motion, Broadcast Motion, and Gather Motion.
-   Other operators: Hash, Sort, Limit, and Append.

## Update statistics information

Statistics information determines whether a suitable execution plan can be generated. You can run the `analyze` command to collect statistics information.

You must first check whether the statistics information contained in explain is correct. In the statistics information, the rows parameter indicates the number of rows of an operator and the width parameter indicates the width of an operator.

If the statistics information is inaccurate, run the `analyze table` command and then try to execute an import task again to obtain statistics information.

**Note:** Run the `analyze` command after you import a large amount of data.

A poor execution plan may be generated if statistics information is not synchronized in time. Example:

Table tmp1 contains 10 million rows of data. Table tmp contains 1000 rows of data. By default, the number of rows of statistics information in Hologres is 1000. The following display results show that the number of rows in tmp1 is inconsistent with the actual value. This indicates that the statistics information has not been updated in time.

```
Seq Scan on tmp1 (cost=0.00..5.01 rows=1000 width=1)
```

When tmp1 and tmp are joined, the expected **explain** result is that tmp1 is displayed above tmp and tmp is used in a Hash Join operation. This is because the data volume in tmp1 is larger than that in tmp. However, statistics information in tmp1 is not updated in time. As a result, tmp1 is used in a Hash Join operation, which leads to low efficiency and an out of memory \(OOM\) exception.

![Example](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8855013061/p162831.png)

Therefore, you must run the `analyze` command to collect statistics information for both tables that are involved in the Join operation.

```
analyze tmp;
analyze tmp1
```

After you run the `analyze` command, the tables that are involved in the Join operation are displayed in the correct locations. tmp1 is displayed above tmp. tmp is used to create a hash table, as shown in the following figure. The number of rows in tmp1 is 10 million. This indicates that the statistics information is updated.

![Location](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p162838.png)

You can also use one of the following methods to check whether statistics information is updated:

-   Query the settings of the analyze\_tuple column in system table hologres.hg\_table\_properties to check whether the number of rows is correct. Alternatively, you can view the value of rows in the Scan node.
-   Query system table hologres.hg\_stats and display its information, such as the histogram of each column, average width, and number of fields with different values, as shown in the following figure.

    ![Other information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p162996.png)


If you encounter the following problems, you must run the `analyze table` command and then execute an import task. This can improve the system efficiency:

-   An OOM exception occurs when multiple tables are joined.

    The `Query executor exceeded total memory limitation xxxxx: yyyy bytes used` error is reported.

-   Import efficiency is low.

    It takes a long time to query or import data in Hologres. This results in a low efficiency.


## Select an appropriate distribution key when you create a table

When you create a table, you can select an appropriate distribution key based on the following principles:

-   Select a join condition column during a JOIN operation as the distribution key.

    If the Motion operation is time-consuming, you can use the colocate with attribute to place multiple tables in the same table group. In addition, set the join key as the distribution key to avoid additional overhead caused by data redistribution when multiple tables are joined. For more information, see [t1884404.dita\#concept\_2479327](/intl.en-US/Best Practices/Performance optimization/Specify the table group and shard count.md).

    ![motion](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p163980.png)

    Sample statements:

    ```
    begin;
    create table tmp(a int, b int, c int);
    call set_table_property('tmp', 'distribution_key', 'a');
    commit;
    
    begin;
    create table tmp1(a int, b int, c int);
    call set_table_property('tmp1', 'distribution_key', 'b');
    commit;
    
    select count(1) from tmp join tmp1 on tmp.a = tmp1.b //Set the join key as the distribution key.
    ```

    By resetting the distribution key, you can distribute data to shards by hash key. In addition, the Redistribute Motion operator, as marked in red in the preceding figure, is optimized. This indicates that data is not redistributed. Therefore, additional network overhead is avoided.

    ![Set the distribution key](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p163985.png)

    Hologres has four types of Motion operators that correspond to four data redistribution scenarios, as listed in the following table.

    |Motion operator type|Description|
    |--------------------|-----------|
    |Redistribute Motion|Data is hash-distributed or randomly distributed to one or more shards.|
    |Broadcast Motion|Data is copied to all shards.Broadcast Motion is superior only if both the number of shards and that of broadcast tables are small. |
    |Gather Motion|Data is summarized to one shard.|
    |Forward Motion|This operator is used for federated queries. Data is transmitted between an external data source or an execution engine and a Hologres execution engine.|

    If the execution result of `explain analyze` shows that the Motion operator is time-consuming, you can redesign the distribution key.

    If a Gather Motion or Broadcast Motion operator is generated due to incorrect statistics information, you can run the `analyze` command to use a more efficient Motion operator.

    You can configure the following parameters to prohibit the generation of Motion operators and then compare the query time of different Motion operators. Sample statements:

    ```
    set optimizer_enable_motion_broadcast = off; // Prohibit the generation of Broadcast Motion.
    set optimizer_enable_motion_redistribute = off; // Prohibit the generation of Redistribute Motion.
    ```

-   Select a column that is frequently used in the Group By clause as the distribution key.

    The Group By Key operation allows data to be redistributed based on the key of the column in the Group By clause. If the Group By operation is time-consuming, you can set the column in the Group By operation as the distribution key.

    ```
    select a from t1 group by a; // Data is hash-redistributed based on the values in column a.
    ```

-   Select a column in which data is evenly distributed and discrete as the distribution key.

    Queries slow down if data is unevenly distributed among multiple shards. You can execute the following statement to check whether the data distribution is even:

    ```
    select count(1) from t1 group by hg_shard_id;
    ```

    If data is unevenly distributed, you must select a column in which data is evenly distributed and discrete as the distribution key.

    **Note:** To change the distribution key, you must create another table and import data.


## Disable Dictionary\_encoding

For STRING-based queries, dictionary encoding or decoding reduces the time consumed to compare strings, but causes a large amount of decoding or encoding overhead.

By default, Hologres establishes Dictionary\_encoding for all STRING-based columns. You can leave Dictionary\_encoding\_columns empty or disable auto dictionary encoding for specific columns.

You can obtain statistics information to check whether the Decode operator is time-consuming. If it is, disable the Decode operator. If a table contains a large number of STRING-type fields, we recommend that you disable STRING-based decoding. Sample statement:

```
begin;
create table tbl (a int not null, b text not null, c int not null, d int);
call set_table_property('tbl', 'dictionary_encoding_columns', '');
commit;
```

## Other optimization items

-   Select an appropriate storage type.

    Hologres supports row-oriented and column-oriented storage modes. You can select a storage mode based on your business scenario. The following table describes the use scenarios and disadvantages of the storage modes.

    |Storage mode|Use scenario|Disadvantage|
    |------------|------------|------------|
    |Row-oriented storage|    -   Point query scenarios in which data is queried with high QPS performance based on primary keys
    -   Scenarios in which all columns of data are read at the same time, and high performance of the UPDATE, DELETE, and INSERT operations is required
|The performance of operations such as large-scale data queries, full table scanning, and aggregation is poor.|
    |Column-oriented storage|Data analysis scenarios such as multi-column data query by range, single-table aggregation, and multi-table join|The performance of the UPDATE and DELETE operations in this mode is poorer than that in row-oriented storage mode. The performance of point queries in this mode is poorer than that in row-oriented storage mode in scenarios with no index specified.|

-   Select an appropriate data type.

    Hologres supports multiple data types. You can select a data type based on your business requirements. The following principles apply:

    -   Select a type that requires minimal storage space.
        -   Preferentially use the INT type rather than the BIGINT type.
        -   Minimize the precision of the DECIMAL type.
        -   We recommend that you do not use the FLOAT type for columns in the Group By operation.
        -   Preferentially use VARCHAR\(n\) rather than CHAR\(n\). The value of n must be as small as possible.
    -   Use consistent data types.

        When you join tables, make sure that the columns that match each other use the same data type. This avoids additional overhead caused by implicit conversion of different data types of columns in Hologres.

    -   Use the DECIMAL data type in operations such as UNION and Group By.

        Operations such as UNION and Group By do not support the DOUBLE PRECISION and FLOAT data types. You must use the DECIMAL type.

-   Select an appropriate primary key.

    Primary keys are used to uniquely identify data. They are applicable to data import scenarios with duplicate primary keys. You can specify option to select a deduplication mode when you import data. Valid values of option:

    -   ignore: Ignore new data.
    -   update. Use new data to overwrite old data.
    Proper settings of primary keys can help the optimizer generate excellent execution plans in specific scenarios, such as scenarios in which `group by pk,a,b,c` is executed for data queries.

    In column-oriented storage mode, configuring primary keys may impose a negative impact on the performance of data writing. In most cases, the performance of data writing without primary keys configured is three times higher than that with primary keys configured.

-   Select an appropriate partitioned table.

    Hologres supports only level-1 partitioned tables. Proper partition settings accelerate queries. You can configure partitions in the following scenarios:

    -   When you delete all the partitions of a subtable, data in partitions of other subtables is not affected. The DROP statement has higher performance than the DELETE statement.
    -   For queries of partition key columns in predicate conditions, you can locate corresponding partitions based on indexes of the partition key columns. You can also directly query subpartitions.
    -   A partitioned table is required for data that is periodically imported in real time. Assume that new data is imported every day. You can use the date column as a partition key column and import data to a subpartition every day. Sample statement:

        ```
        begin;
        create table insert_partition(c1 bigint not null, c2 boolean, c3 float not null, c4 text, c5 timestamptz not null) partition by list(c4);
        call set_table_property('insert_partition', 'orientation', 'column');
        commit;
        create table insert_partition_child1 partition of insert_partition for values in('20190707');
        create table insert_partition_child2 partition of insert_partition for values in('20190708');
        create table insert_partition_child3 partition of insert_partition for values in('20190709');
        
        select * from insert_partition where c4 >= '20190708';
        select * from insert_partition_child3;
        ```

-   Select an appropriate index.

    Hologres uses various types of indexes to implement different features. You can select an index based on your business scenario to improve the query performance. The following table lists the supported indexes.

    |Type|Name|Description|Usage recommendation|Sample query statement|
    |----|----|-----------|--------------------|----------------------|
    |Clustering\_key|Clustered index|Used in files. Data in a file is sorted by this index.For some range queries, Hologres can use data orderliness attributes of the clustered index to filter data.

|Use a range query or Filter query column as the clustered index column.|`select sum(a) from tb1 where a > 100 and a < 200;`|
    |Bitmap Columns|Bitmap index|Used in files. Bitmaps are created for data in a file based on this index.For equivalent queries, Hologres can encode data of each row by value, and perform bit operations to quickly locate the rows where data resides. The time complexity is O\(1\).

|Use an equivalent query column as the bitmap index.|`select * from tb1 where a =100;`|
    |Segment\_key|Segment key|A file-level index. Data is divided into files by this index.You can use this index to quickly locate the destination file.

|Use this index to quickly filter files. Then, use the bitmap index or clustered index for range or equivalent queries in files.We recommend that you use a common equivalent or range query column as the segment key.

|`select sum(a) from tb1 where ts > '2020-01-01' and a < '2020-03-02';`|

    Both the clustered index and segment key must comply with the leftmost prefix matching principle of traditional databases such as MySQL. That is, sort data based on the values at the leftmost column of an index. If the values at the leftmost column are ordered, use the second leftmost column for data sorting. Example:

    ```
    call set_table_property('tmp', 'clustering_key', 'a,b,c');
    select * from tmp where a > 1   // The clustered index can be used.
    select * from tmp where a > 1 and c > 2   // The clustered index can be used only for a.
    select * from tmp where a > 1 and b > 2   //The clustered index can be used for both a and b.
    select * from tmp where a > 1 and b > 2 and c > 3 // The clustered index can be used for a, b, and c.
    select * from tmp where b > 1 and c > 2   // The clustered index cannot be used for b or c.
    ```

    A bitmap index supports AND and OR queries among multiple columns. Example:

    ```
    call set_table_property('tmp', 'bitmap_columns', 'a,b,c');
    select * from tmp where a = 1 and b = 2   //A bitmap index can be used.
    select * from tmp where a = 1 or b = 2   // A bitmap index can be used.
    ```

    **Note:** You can add a bitmap index after you create a table. The clustered index and segment key are specified when you create a table and cannot be added subsequently.

-   Optimize single-stage aggregation to multi-stage aggregation.

    If aggregation-related operators are time-consuming, check whether local shard-level pre-aggregation is performed.

    You can perform aggregation operations on a single shard to decrease the volume of data involved in the final aggregation operation and improve performance.

    -   Three-stage aggregation: Aggregate data by file, aggregate data in each shard, and then summarize the aggregation results of all shards.

        ![Three-stage aggregation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p164212.png)

    -   Two-stage aggregation: Aggregate data in each shard, and then summarize the aggregation results of all shards.

        ![Two-stage aggregation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p164213.png)

    You can execute the following statement to forcibly use Hologres to perform multi-stage data aggregation:

    ```
    set optimizer_force_multistage_agg = on;
    ```


## Optimize SQL statements

You can optimize SQL statements to improve the query efficiency.

-   Avoid federated queries.

    If an SQL statement contains operators that are not supported by a Hologres execution engine, the system sends the operators to a Postgres execution engine. In this case, the query performance is poor. You must optimize the SQL statement.

    Example

    Hologres does not support apply and nestloop left anti semi join operations. In this case, the system uses a Postgres execution engine to perform the not in operation. In addition, the system changes not in to not exists. SQL statement before optimization:

    ```
    explain select * from tmp where a not in (select a from tmp1);
    ```

    The External operator indicates that the SQL statement is executed in the Postgres execution engine.

    ![post](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p164218.png)

    SQL statement after optimization:

    ```
    explain select * from tmp where not exists (select a from tmp1 where a = tmp.a);
    ```

    ![SQL statement after optimization](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0955013061/p164219.png)

-   Avoid fuzzy search.

    No index is created in a fuzzy search operation, such as Like.

-   Disable query result caching.

    Hologres caches the same query or subquery results by default. Repeated queries may trigger the caching feature. You can disable the caching feature to avoid any impact on performance tests. Sample statement:

    ```
    set hg_experimental_enable_result_cache = off;
    ```


## Obtain updated statistics information

Each execution operator in the Hologres computing model contains the `open()` and `getNext()` operations. These two types of operations allow data to be streamed among operators from bottom to top.

You can execute the `explain analyze` statement to obtain detailed information throughout the execution of a query.

If the execution of your query is slow, you can use the `explain analyze` statement to identify the operators that are time-consuming. Sample statement:

```
explain analyze select sum(a) from tmp group by a;
```

The execution information of a query is displayed in a pair of brackets \(\[\]\) in each operator. The following figure shows the sample results.

![Updated statistics information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p163774.png)

The following table lists the parameters included in the execution results of `explain analyze`.

|Parameter|Description|
|---------|-----------|
|Row\_count|The number of rows generated by the current operator.|
|Count|The number of files scanned by the current operator.|
|Record\_batch\_producer|The amount of data generated by the current operator.|
|\(Max/Min/Avg\)\_open\_time|The approximate time that is taken to initialize the current operator.|
|\(Max/Min/Avg\)\_get\_next\_time|The approximate time that the current operator spends in waiting for the data from the downstream operator.|
|Physical\_reads|The number of I/O operations of the current operator.|
|Count|The number of files opened by the current operator.|
|Record\_batches\_produced|The output size recorded by the current operator.|

The following table lists the operations that are time-consuming throughout the execution of a query plan.

|Operation|Description|
|---------|-----------|
|Get the first block cost:|The time that is taken for the topmost node to retrieve the first block during the execution of a query plan at the backend.|
|Get result cost:|The overall time that is taken for the frontend to obtain the data returned from the backend.|
|Start query cost:|The time that is taken for the frontend to distribute execution plans.|
|Optimizer cost:|The time that is taken for the frontend optimizer to optimize SQL query statements.|

In the sample results, it takes more time for the frontend to obtain data from the backend and more time is spent on aggregation operations. You can optimize the query performance by using the generated statistics information.

