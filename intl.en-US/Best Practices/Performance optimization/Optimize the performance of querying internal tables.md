---
keyword: [best practice, optimize the performance of querying internal tables, Hologres]
---

# Optimize the performance of querying internal tables

This topic describes the best practice for optimizing the performance of querying internal tables in Hologres.

## Update statistics information

Statistics information determines whether a suitable execution plan can be generated. For example, Hologres collects statistics on sample data to analyze the data to be queried from the following aspects: data distribution and characteristics, table statistics, column statistics, number of rows, number of columns, column width, cardinality, frequency, maximum value, minimum value, long key value, and bucket distribution and characteristics. The statistics can help update the estimated cost for operator execution, narrow down the query range, and estimate the optimal join order, the memory overhead, and the degree of parallelism \(DOP\). This way, the optimizer can generate a better execution plan. For more information about the statistics, see [Using Explain](https://www.postgresql.org/docs/11/using-explain.html).

Statistics collection has specific limits. **In scenarios where statistics are collected in a non-real-time manner, for example, statistics collection is manually or periodically triggered, you may fail to obtain the most accurate data characteristics.** You must first run the explain command and check whether the statistics information in the execution result of the explain command is correct. In the statistics information, the rows parameter indicates the number of rows of an operator and the width parameter indicates the width of an operator.

A poor execution plan may be generated if statistics information is not synchronized in time. The following example is for your reference:

-   Table tmp1 contains 10 million rows of data. Table tmp contains 1,000 rows of data. In Hologres, the default value of the rows parameter is 1000 in statistics information. The following execution result of the explain command indicates that the number of rows in Table tmp1 is inconsistent with the actual value. This indicates that the statistics information is not updated in time.

    ```
    Seq Scan on tmp1 (cost=0.00..5.01 rows=1000 width=1)
    ```

    ![Example](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8855013061/p162831.png)

-   When Table tmp1 and Table tmp are joined, the expected result of the **explain** command is that Table tmp1 is processed before Table tmp and Table tmp is used in a HASH JOIN operation. This is because the data volume of Table tmp1 is larger than that of Table tmp. However, statistics information in Table tmp1 is not updated in time. As a result, Table tmp1 is used in a HASH JOIN operation, which leads to low efficiency and an out of memory \(OOM\) exception. Therefore, you must run the `analyze` command to collect statistics information for both tables that are involved in the JOIN operation.

    ```
    analyze tmp;
    analyze tmp1
    ```

    After you run the `analyze` command, the tables that are involved in the JOIN operation are processed in the correct sequence. The following figure shows the execution result of the explain command after the analyze command is run. Table tmp1 is processed before Table tmp. Table tmp is used to create a hash table. The number of rows in Table tmp1 is 10 million. This indicates that the statistics information is updated.

    ![Sequence](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p162838.png)


If `rows=1000` is displayed for Table tmp1 in the execution result of the explain command, statistics information is not synchronized to the optimizer. Poor query performance is usually caused by the lack of statistics information. You can run the `analyze` command to collect statistics information and synchronize the information to the optimizer in time.

We recommend that you run the `analyze` command in the following scenarios:

-   Data is loaded.
-   A large number of INSERT, UPDATE, and DELETE operations are performed.
-   Statistics information needs to be collected for both internal and external tables.
-   Statistics information needs to be collected for a parent partitioned table.
-   If you encounter the following problems, you must run the `analyze` command before you run an import task. This can systematically improve efficiency.

-   An OOM exception occurs when multiple tables are joined.

    The `Query executor exceeded total memory limitation xxxxx: yyyy bytes used` error is reported.

-   Import efficiency is low.

    When you query or import data in Hologres, tasks cannot end in time. This results in low efficiency.


You can check whether statistics information is updated by using one of the following methods:

-   Query the settings in the analyze\_tuple column in the system table named hologres.hg\_table\_properties to check whether the number of rows is correct. Alternatively, you can view the value of the rows parameter in the Scan node.
-   Query the system table named hologres.hg\_stats and display its information, such as the histogram of each column, average width, and number of distinct values, as shown in the following figure.

    ![Others](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p162996.png)


## View and change the number of shards after scale-up

The number of shards affects the DOP of queries. Its setting is critical to query performance. If only a few shards are available, the DOP is low. Excessive shards may lead to increased query startup overhead and reduced query efficiency. You can improve query efficiency by setting an appropriate number of shards based on the instance specifications.

The number of shards is pre-allocated based on the instance type of a Hologres instance. The default number of shards for an instance equals the number of CPU cores that are used for core queries in the instance. The number of CPU cores that are used for core queries is slightly smaller than the number of purchased CPU cores. The purchased CPU cores are allocated to different nodes such as query nodes, access nodes, control nodes, and scheduling nodes. For more information about the default numbers of shards for instances of different instance types, see [Instance types](/intl.en-US/Manage Instances/Instance types.md). After an instance is scaled up, the number of shards is not automatically changed. You can change the number of shards based on your business requirements.

You must change the number of shards in the following scenarios:

-   After a Hologres instance is scaled up, the business grows, and the number of shards cannot meet the business requirements. You need to improve query efficiency for the business. In this case, you can create a table group and specify a larger number of shards for the table group. The table and data are still in the original table group. You need to import them to the new table group for resharding.
-   After a Hologres instance is scaled up, new business needs to be launched, and the original business cannot be affected. In this case, you can create a table group and specify an appropriate number of shards for the table group. You cannot modify the schema of the original table.

**Note:** You can create multiple table groups in a database. Make sure that the total number of shards for all table groups does not exceed the default number of shards recommended for the instance. This way, you can make the most of CPU resources.

To change the number of shards, perform the followings steps:

1.  Query a table group.

    Query the table group to which a table belongs. The number of shards is the same for all tables in a table group. In the following sample statement, you must replace `table_name` with the name of the table that you want to query.

    ```
    select property_value from hologres.hg_table_properties where table_name = 'tmp' and property_key = 'table_group';
    ```

2.  Query the number of shards for the table group.

    In the query result, the value of the `is_default_tg` parameter indicates whether the table group is the default table group and that of the `shard_count` parameter indicates the number of shards.

    ```
    select * from hologres.hg_table_group_properties;
    ```

    ![Number of shards](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1307790261/p239859.png)

3.  Change the number of shards.

    You can execute the following statement to change the number of shards for the table group based on your business requirements:

    ```
    call hg_update_database_property('shard_count', '60');
    ```

    After the statement is executed, a table group is created and is set as the default table group of the current database. The number of shards is 60 for this table group. Take note of the following items:

    -   All newly created tables belong to this table group unless you execute the preceding statement again to create another table group.
    -   The original table and data belong to the original table group and are not migrated to the new table group.
    -   The original table group becomes ineffective only after the original table and data are migrated to the new table group or deleted.

## Select an appropriate distribution key when you create a table

A distribution key is used to evenly divide data into multiple shards. This can help avoid data skew. If you specify the distribution keys of multiple tables as the join keys to perform a local JOIN operation on the tables, queries can be accelerated. When you create a table, you can select an appropriate distribution key based on the following principles:

-   Specify the join keys as the distribution keys.

    For example, the execution result of the explain command indicates that the execution plan for querying Table tmp and Table tmp1 contains a redistribution operation, but no local JOIN operation. As a result, query efficiency is low. You need to re-create the two tables and specify the distribution keys as the join keys. This avoids the extra overhead caused by data redistribution when multiple tables are joined.

    ![motion](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p163980.png)

    You can execute the following DDL statements to re-create the two tables and specify the distribution keys as the join keys:

    ```
    begin;
    create table tmp(a int, b int, c int);
    call set_table_property('tmp', 'distribution_key', 'a');
    commit;
    
    begin;
    create table tmp1(a int, b int, c int);
    call set_table_property('tmp1', 'distribution_key', 'b');
    commit;
    
    select count(1) from tmp join tmp1 on tmp.a = tmp1.b // Specify the distribution keys as the join keys. 
    ```

    After you reset the distribution keys for the two tables, run the explain command again. The execution result of the command indicates that data is evenly distributed to shards by hash key. In addition, the Redistribute Motion operator, as framed in red in the preceding figure, is optimized. This indicates that data is not redistributed. Therefore, additional network overhead is avoided.

    ![Set the distribution key](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p163985.png)

    Hologres has four types of Motion operators that correspond to four data redistribution scenarios, as described in the following table.

    |Motion operator type|Description|
    |--------------------|-----------|
    |Redistribute Motion|Data is hash-distributed or randomly distributed to one or more shards.|
    |Broadcast Motion|Data is copied to all shards. The Broadcast Motion operator is superior only in scenarios where both the number of shards and that of broadcast tables are small. |
    |Gather Motion|Data is summarized to one shard.|
    |Forward Motion|This operator is used for federated queries. Data is transmitted between an external data source or an execution engine and a Hologres execution engine.|

    When you view the execution result of the explain command, take note of the following items:

    -   If the Motion operator is time-consuming, you can redesign the distribution key.
    -   The Broadcast Motion operator is superior only in scenarios where both the number of shards and that of broadcast tables are small.
    -   If a Gather Motion or Broadcast Motion operator is generated due to invalid statistics information, you can run the `analyze` command to use the more efficient Redistribute Motion operator.
    -   You can execute the following statements to prohibit the generation of Motion operators and then compare the query time of different Motion operators:

        ```
        set optimizer_enable_motion_broadcast = off; -- Prohibit the generation of Broadcast Motion. 
        set optimizer_enable_motion_redistribute = off; -- Prohibit the generation of Redistribute Motion. 
        ```

-   Select a column that is frequently used in the GROUP BY clause as the distribution key.

    The GROUP BY Key operation allows data to be redistributed based on the key of the column in the GROUP BY clause. If the GROUP BY operation is time-consuming, you can set the column in the GROUP BY clause as the distribution key.

    ```
    select a from t1 group by a; -- Data is hash-redistributed based on the values in Column a. 
    ```

-   Select a column in which data is evenly distributed and discrete as the distribution key.

    Queries slow down if data is unevenly distributed among multiple shards. You can execute the following statement to check whether data is evenly distributed:

    ```
    select count(1) from t1 group by hg_shard_id;
    ```

    If data is unevenly distributed, you must select a column in which data is evenly distributed and discrete as the distribution key.

    **Note:** To change the distribution key, you must create another table and import data.


## Disable dictionary encoding

For STRING-based queries, dictionary encoding or decoding reduces the time that is consumed to compare strings, but causes a large amount of decoding or encoding overhead.

By default, Hologres enables dictionary encoding for all columns of the STRING type. You can leave the dictionary\_encoding\_columns property empty or disable dictionary encoding for specific columns.

You can obtain statistics information to check whether the Decode operator is time-consuming. If it is, disable the Decode operator. If a table contains a large number of columns of the STRING type, we recommend that you disable dictionary encoding to improve performance.

If a table contains a large number of columns of the STRING type, you can determine whether to enable dictionary encoding for the columns based on your business requirements. The following sample statements are for your reference:

```
begin;
create table tbl (a int not null, b text not null, c int not null, d int);
call set_table_property('tbl', 'dictionary_encoding_columns', '');
commit;
```

## Other optimization items

-   Select an appropriate storage type.

    Hologres supports row-oriented and column-oriented storage modes. You can select a storage mode based on your business scenario. The following table describes the storage modes.

    |Storage mode|Scenario|Disadvantage|
    |------------|--------|------------|
    |Row-oriented storage|    -   Point query scenarios in which data is queried with high QPS performance based on primary keys.
    -   Scenarios in which all columns of data are read at the same time, and high performance of the UPDATE, DELETE, and INSERT operations is required.
|The performance of operations such as large-scale data queries, full table scanning, and aggregation is poor.|
    |Column-oriented storage|Data analysis scenarios such as multi-column data query by range, single-table aggregation, and multi-table join.|The performance of the UPDATE and DELETE operations in this mode is poorer than that in row-oriented storage mode. The performance of point queries in this mode is poorer than that in row-oriented storage mode in scenarios without specified indexes.|

-   Select an appropriate data type.

    Hologres supports multiple data types. You can select a data type based on your business requirements. The following principles apply:

    -   Select a type that requires minimal storage space.
        -   Preferentially use the INT type rather than the BIGINT type.
        -   Minimize the precision of the DECIMAL type.
        -   We recommend that you do not use the FLOAT type for columns in the GROUP BY clause.
        -   Preferentially use the TEXT type rather than the VARCHAR\(n\) and CHAR\(n\) types. The value of n must be as small as possible.
        -   Use the TIMESTAMPTZ and DATE types rather than the TEXT type to represent a date.
    -   Use consistent data types.

        When you join tables, make sure that the columns that match each other use the same data type. This avoids additional overhead that is caused if Hologres implicitly converts the data type for columns with different data types.

    -   Use the DECIMAL type in operations such as UNION and GROUP BY.

        Operations such as UNION and GROUP BY do not support the DOUBLE PRECISION and FLOAT types. You must use the DECIMAL type.

-   Select an appropriate primary key.

    Primary keys are used to uniquely identify data. They are applicable to data import scenarios with duplicate primary key values. You can specify option to select a deduplication mode when you import data. Valid values of option:

    -   ignore: ignores new data.
    -   update: uses new data to overwrite old data.
    Proper settings of primary keys can help the optimizer generate excellent execution plans in specific scenarios, such as scenarios in which `group by pk,a,b,c` is executed for data queries.

    In column-oriented storage mode, configuring primary keys may impose a negative impact on the performance of data writing. In most cases, the performance of data writing without configured primary keys is three times higher than that with configured primary keys.

-   Select an appropriate partitioned table.

    Hologres tables support only one level of partitioning. Proper partition settings accelerate queries.

    **Note:** For data imported by day, we recommend that you create a partitioned table for the data of each day. The data is separately stored. Only the data of the current day is accessed.

    You can configure partitions in the following scenarios:

    -   When you delete all the partitions of a child partitioned table, data in partitions of other child partitioned tables is not affected. The DROP statement has higher performance than the DELETE statement.
    -   For queries of partition key columns in predicate conditions, you can find corresponding partitions based on indexes of the partition key columns. You can also directly query child partitions.
    -   A partitioned table is required for data that is periodically imported in real time. If new data is imported every day, you can use the date column as a partition key column and import data to a child partition every day. The following sample statements are for your reference:

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

    Hologres uses various types of indexes to implement different features. You can select an index based on your business scenario to improve query performance. The following table describes the supported indexes.

    |Index type|Index name|Description|Usage recommendation|Sample query statement|
    |----------|----------|-----------|--------------------|----------------------|
    |Clustering\_key|Clustered index|Used in files. Data in a file is sorted by this index. For part of range queries, Hologres can use data orderliness attributes of the clustered index to filter data.

|Use a range query or filter query column as the clustered index. Index-based data filtering complies with the leftmost prefix matching principle. We recommend that you specify no more than two columns.|`select sum(a) from tb1 where a > 100 and a < 200;`|
    |Bitmap Columns|Bitmap index|Used in files. Bitmaps are created for data in a file based on this index. For equivalent queries, Hologres can encode data of each row by value, and perform bit operations to find the rows where data resides. The time complexity is O\(1\).

|Use an equivalent query column as the bitmap index.|`select * from tb1 where a =100;`|
    |Segment\_key \(also known as event\_time\_column\)|Segment key|A file-level index. Data is written to files in Append Only mode. Then, small files are merged based on the index. A segment key identifies the boundary of a file. You use the segment key to find the required file.

A segment key is designed for ordered, range-specific data such as timestamps and dates. Therefore, it has a strong correlation with the time when data is written.

|Use this index to filter files. Then, use the bitmap index or clustered index for range or equivalent queries in files. Index-based data filtering complies with the leftmost prefix matching principle. We recommend that you specify only one column. We recommend that you specify the first non-empty timestamp field as the segment key.

|`select sum(a) from tb1 where ts > '2020-01-01' and a < '2020-03-02';`|

    Both the clustered index and segment key must comply with the leftmost prefix matching principle of traditional databases such as MySQL. This way, data is sorted based on the values in the leftmost column of an index. If the values in the leftmost column are ordered, use the second leftmost column to sort data. The following sample statements are for your reference:

    ```
    call set_table_property('tmp', 'clustering_key', 'a,b,c');
    select * from tmp where a > 1 -- The clustered index can be used. 
    select * from tmp where a > 1 and c > 2 -- The clustered index can be used only for Column a. 
    select * from tmp where a > 1 and b > 2 -- The clustered index can be used for both Column a and Column b. 
    select * from tmp where a > 1 and b > 2 and c > 3 -- The clustered index can be used for Column a, Column b, and Column c. 
    select * from tmp where b > 1 and c > 2 -- The clustered index cannot be used for Column b or Column c. 
    ```

    A bitmap index supports AND and OR queries among multiple columns. The following sample statements are for your reference:

    ```
    call set_table_property('tmp', 'bitmap_columns', 'a,b,c');
    select * from tmp where a = 1 and b = 2 -- The bitmap index can be used. 
    select * from tmp where a = 1 or b = 2 -- The bitmap index can be used. 
    ```

    **Note:** You can add a bitmap index after you create a table. The clustered index and segment key are specified when you create a table and cannot be subsequently added.


## Check whether an index is used

Create a table named tmp and specify index fields by executing the following statements:

```
begin;
create table tmp(a int not null, b int not null, c int not null);
call set_table_property('tmp', 'clustering_key', 'a');
call set_table_property('tmp', 'segment_key', 'b');
call set_table_property('tmp', 'bitmap_columns', 'a,b,c');
commit;
```

-   To check whether the clustered index is used, run the following command:

    ```
    explain select * from tmp where a > 1;
    ```

    ![cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7297262261/p185561.png)

-   To check whether the bitmap index is used, run the following command:

    ```
    explain select * from tmp where c = 1;
    ```

    ![bitmap](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7297262261/p185563.png)

-   To check whether the segment key is used, run the following command:

    ```
    explain select * from tmp where b > 1;
    ```

    ![segment](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7297262261/p185594.png)


## Optimize single-stage aggregation to multi-stage aggregation

If aggregate operators are time-consuming, check whether local shard-level pre-aggregation is performed.

You can perform aggregation operations on a single shard to decrease the volume of data that is involved in the final aggregation operation and improve performance. The following aggregation operations are supported:

-   Three-stage aggregation: Aggregate data by file, aggregate data in each shard, and then summarize the aggregation results of all shards.

    ![Three-stage aggregation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p164212.png)

-   Two-stage aggregation: Aggregate data in each shard and summarize the aggregation results of all shards.

    ![Two-stage aggregation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p164213.png)


You can execute the following statement to forcibly use Hologres to perform multi-stage data aggregation:

```
set optimizer_force_multistage_agg = on;
```

## Optimize SQL statements

You can optimize SQL statements to improve query efficiency by using the following methods:

-   Avoid federated queries.

    If an SQL statement contains operators that are not supported by a Hologres execution engine, the system sends the operators to a PostgreSQL execution engine. In this case, query performance is poor. You must optimize the SQL statement. The following example demonstrates how to optimize an SQL statement:

    Hologres does not support the not in operation. In this case, the system uses a PostgreSQL execution engine to perform the not in operation. In addition, the system changes not in to not exists. Before optimization, run the following command to check the query plan for the SQL statement:

    ```
    explain select * from tmp where a not in (select a from tmp1);
    ```

    The External operator indicates that the SQL statement is executed in the PostgreSQL execution engine.

    ![post](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9855013061/p164218.png)

    After optimization, run the following command to check the query plan for the SQL statement:

    ```
    explain select * from tmp where not exists (select a from tmp1 where a = tmp.a);
    ```

    ![SQL statement after optimization](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0955013061/p164219.png)

-   Avoid fuzzy search.

    No index such as Like is created in a fuzzy search operation.

-   Disable query result caching.

    By default, Hologres caches the same query or subquery results. Repeated queries may trigger the caching feature. You can disable the caching feature to avoid a negative impact on performance tests. The following sample statement is for your reference:

    ```
    set hg_experimental_enable_result_cache = off;
    ```


