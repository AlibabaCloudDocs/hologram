# Aggregate views

Aggregate views allow you to pre-aggregate the data in the fixed columns of a single table. On the basis of pre-aggregation and automatic query rewriting, you can use data pre-aggregation results to accelerate statistical queries. This topic describes how to use aggregate views in Hologres.

## Limits

When you use aggregate views in Hologres, take note of the following limits:

-   This feature is supported only in Hologres V0.10 and later. You can check the version of your Hologres instance on the instance details page in the Hologres console. If the version of your instance is earlier than V0.10, [submit a ticket](https://workorder-intl.console.aliyun.com/) to update your instance.
-   Before you use aggregate views, you must execute the following statement to install an extension in a database as the superuser. An extension is installed at the database level. For each database, you need to install an extension only once. If you create a database, you must execute the statement again.

    ```
    create extension  aggregate_view;
    ```


## Create an aggregate view

You can use the hg\_aggregate\_view\_create function to create an aggregate view. This section describes how to create an aggregate view.

-   **Syntax**

    ```
    call hg_aggregate_view_create(
        aggregate_dst_view , /* The name of the aggregate view. */
        aggregate_src_table , /* The name of the table whose data is to be aggregated. */
        aggregate_keys_non_time , /* The aggregate keys based on which data is aggregated. These keys do not specify the time. */
        aggregate_key_time , /* The aggregate key that specifies the time based on which data is aggregated. */
        aggregate_values , /* The columns whose values are to be aggregated. Specify the columns in sequence. Make sure that the specified columns are not duplicated. */
        aggregate_funcs , /* The aggregate functions. The following aggregate functions are supported: sum, min, max, count, avg, and approx_count_distinct. */
        aggregate_timestamp_column , /* The column that specifies the accurate time based on which the aggregate view is refreshed. The column must be of the TIMESTAMPTZ type. */
        aggregate_allowed_lateness , /* The maximum intervals at which the aggregate view is refreshed. */
        aggregate_base_table_ttl /* The time to live (TTL) of the data in the aggregate view. */)
    ```

-   **Parameters**

    |Parameter|Type|Description|
    |---------|----|-----------|
    |aggregate\_dst\_view|text|The name of the aggregate view.|
    |aggregate\_src\_table|text|The name of the table whose data is to be aggregated.|
    |aggregate\_keys\_non\_time|text\[\]|The aggregate keys based on which data is aggregated. These keys do not specify the time.|
    |aggregate\_key\_time|text|The aggregate key that specifies the time based on which data is aggregated. This key also specifies the time range to query.|
    |aggregate\_values|text\[\]|The columns whose values are to be aggregated. Specify the columns in sequence. Make sure that the specified columns are not duplicated.|
    |aggregate\_funcs|text\[\]|The aggregate functions. The following aggregate functions are supported:    -   sum: calculates the total value.
    -   min: selects the minimum value.
    -   max: selects the maximum value.
    -   count: counts the number.
    -   avg: calculates the average value.
    -   approx\_count\_distinct: counts the approximate number of rows that contain distinct values in a column. The return value is of the BINARY type. You must call the approx\_count\_distinct\_final function to return a value. You can first use the approx\_count\_distinct\_merge function to aggregate all values.
    -   rb\_count\_distinct: counts the number of rows that contain distinct values in a column. The input values must be of the INT32 type. The return value is of the BINARY type. You must call the rb\_cardinality function to return a value. You can first use the rb\_or\_agg function to aggregate all values. Before you use these functions, you must install an extension for roaring bitmaps by executing the following statement:

        ```
 create extension roaringbitmap_hqe;
        ``` |
    |aggregate\_timestamp\_column|text|The column that specifies the accurate time based on which the aggregate view is refreshed. This column must be non-empty in the source table.|
    |aggregate\_allowed\_lateness|interval|The maximum intervals at which the aggregate view is refreshed. This parameter must be used together with the aggregate\_timestamp\_column parameter. If you refresh an aggregate view without specifying the cut-off time, the cut-off time is calculated based on the source table by using the following formula: max\(aggregate\_timestamp\_column\) - aggregate\_allowed\_lateness.|
    |aggregate\_base\_table\_ttl|int|The TTL of the data in the aggregate view. Unit: seconds. When the specified TTL expires, the data is cleared from the aggregate view.|

-   **Examples**

    The following table lists the sample data that is used in this example.

    |event\_id|uid|user\_id|event\_time|province|city|PV|charge|event\_count|
    |---------|---|--------|-----------|--------|----|--|------|------------|
    |1002|User10011|10011|2020-11-11 00:01:11|hebei|tangshan|1|10|10|
    |1002|User10012|10012|2020-11-11 00:01:12|shandong|qingdao|2|11|11|
    |1003|User10013|10013|2020-11-11 00:01:13|hunan|changsha|2|11.9|11|
    |1002|User10012|10012|2020-11-11 00:01:14|shandong|qingdao|1|1.1|9|
    |1002|User10012|10012|2020-11-11 00:01:14|shandong|qingdao|1|1.1|9|

    The following sample code provides an example on how to create a table based on the sample data:

    ```
    -- Create a table and insert the sample data.
    CREATE TABLE agg_src_table
    (
        event_id INT
        ,user_id TEXT
        ,uid INT
        ,event_time timestamptz NOT NULL
        ,province TEXT
        ,city TEXT
        ,pv INT
        ,charge NUMERIC(15,2)
        ,event_count INT
    );
    
    insert into agg_src_table values 
    (1002, 'User10011', 10011, '2020-11-11 00:01:11', 'hebei', 'tangshan', 1, 10,10 ),
    (1002, 'User10012', 10012, '2020-11-11 00:01:12', 'shandong', 'qingdao', 2, 11,11 ),
    (1003, 'User10013', 10013, '2020-11-11 00:01:13', 'hunan', 'changsha', 2, 11.9, 11 ),
    (1002, 'User10012', 10012, '2020-11-11 00:01:14', 'shandong', 'qingdao', 1, 1.1, 9 ),
    (1002, 'User10012', 10012, '2020-11-11 00:01:14', 'shandong', 'qingdao', 1, 1.1, 9 );
    
    -- Create a view for the table to pre-aggregate the data and truncate the values of the event_time field to be accurate to the hour. 
    CREATE view agg_src_view AS
    SELECT  province
            ,city
            ,event_time
            ,date_trunc('hour', event_time) AS event_date
            ,pv
            ,charge
            ,event_count
            ,user_id
            ,uid
    FROM    agg_src_table ;
    ```

    The following sample code provides an example on how to create an aggregate view:

    ```
    -- Install an extension for aggregate views.
    create extension  aggregate_view;
    
    -- Create an aggregate view.
    call hg_aggregate_view_create(
      'agg_dst_view', -- The name of the aggregate view.
      'agg_src_view', -- The name of the table whose data is to be aggregated. In this example, the view that you created for the table is used.
      ARRAY['province', 'city'], -- The aggregate keys. In this example, data is aggregated based on the province and city fields.
      'event_date', -- The aggregate key that specifies the time based on which data is aggregated.
      ARRAY['event_count', 'charge', 'pv', 'user_id'], 
      ARRAY['count','avg', 'sum', 'approx_count_distinct'], -- The aggregate functions. In this example, the count function is used for the event_count field, the avg function is used for the charge field, the sum function is used for the pv field, and the approx_count_distinct function is used for the user_id field. 
      'event_time', -- The column that specifies the accurate time based on which the aggregate view is refreshed.
      '600 sec', -- The maximum intervals at which the aggregate view is refreshed. In this example, the maximum refresh interval is 600 seconds.
      '2592000' -- The TTL of the data in the aggregate view.
        );
        
    -- Query data in the aggregate view.
    SELECT * FROM agg_dst_view
    ```


## Refresh an aggregate view

When you refresh an aggregate view, you can update the data that is written in real time to the source table. Hologres allows you to refresh an aggregate view by cut-off time and by time range. You can run only one refresh task for an aggregate view at a time. This section describes how to refresh an aggregate view.

-   **By cut-off time**
    -   Function description

        You can specify a cut-off time to refresh an aggregate view by updating the data that is written before the cut-off time. By default, the incremental update mode is used. If you select the incremental update mode, the data that is written from the last cut-off time to the current cut-off time is updated. If you select the full update mode, all data that is written before the cut-off time is updated. Syntax:

        ```
        call hg_aggregate_view_refresh('aggregate_dst_view', 'refresh_until_timestamp','delta');
        ```

    -   Parameters

        |Parameter|Type|Description|
        |---------|----|-----------|
        |aggregate\_dst\_view|text|The name of the aggregate view.|
        |refresh\_until\_timestamp|timestamptz|The timestamp that specifies the cut-off time to query the data to be updated. The data records whose values in the column specified by the aggregate\_timestamp\_column parameter are less than the value of this parameter are updated in the aggregate view. By default, the value of the refresh\_until\_timestamp parameter is calculated by using the following formula: max\(aggregate\_timestamp\_column\) - aggregate\_allowed\_lateness.|
        |delta|bool|Specifies whether to update incremental data. Valid values:         -   true: updates incremental data. The data that is written from the last cut-off time to the current cut-off time is updated. The default value is true.
        -   false: updates full data. All data that is written before the cut-off time is updated. The data is cleared from the aggregate view before it is refreshed based on the updated data. |

    -   Examples

        Update the data that is written before 2020-11-11 01:00:00.

        ```
        -- Insert data into the source table.
        insert into agg_src_table values 
        (1002, 'User10998', 10320, '2020-11-11 00:01:15', 'sichuan', 'chengdu', 2, 3,1 ),
        (1003, 'User10007', 10343, '2020-11-12 00:01:15', 'zhejiang', 'hangzhou', 7, 2,4 ),
        (1003, 'User10073', 10221, '2020-11-12 00:01:16', 'anhui', 'huangshan', 3, 8,4 );
        
        -- Update incremental data.
        call hg_aggregate_view_refresh('agg_dst_view', '2020-11-11 01:00:00'::timestamptz,'true');
        
        -- Update full data.
        call hg_aggregate_view_refresh('agg_dst_view', '2020-11-11 00:00:00'::timestamptz,'false');
        ```

-   **By time range**
    -   Function description

        You can specify a time range to refresh an aggregate view by updating the data that is written within the specified time range. Syntax:

        ```
        call hg_aggregate_view_range_refresh('aggregate_dst_view','refresh_start_timestamp','refresh_until_timestamp');   
        ```

    -   Parameters

        |Parameter|Type|Description|
        |---------|----|-----------|
        |aggregate\_dst\_view|text|The name of the aggregate view.|
        |refresh\_until\_timestamp|timestamptz|The timestamp that specifies the end of the time range to query the data to be updated.|
        |refresh\_start\_timestamp|timestamptz|The timestamp that specifies the beginning of the time range to query the data to be updated.|

    -   Examples

        Update the data that is written from 2020-11-11 01:00:00 to 2020-11-12 01:00:00.

        ```
        -- Update the data that is written within the specified time range. If you refresh an aggregate view without specifying the time range, the time range is from the result of the current time minus the maximum refresh interval to the current time.
        call hg_aggregate_view_range_refresh('agg_dst_view','2020-11-11 01:00:00'::timestamptz,'2020-11-12 01:00:00'::timestamptz);
        ```


## Delete an aggregate view

You can delete an aggregate view that is no longer used.

-   Syntax

    ```
    call hg_aggregate_view_delete('aggregate_dst_view');   
    ```

-   Parameters

    aggregate\_dst\_view: the name of the aggregate view that you want to delete.

-   Examples

    Delete the aggregate view named agg\_dst\_view.

    ```
    call hg_aggregate_view_delete('agg_dst_view');
    ```


## Examples

In this example, the access data of a website is used. To manage the access data, you can use an aggregate view. For example, you can approximately and accurately remove duplicate values for user IDs, count the number of access events, and calculate the average amount of charges and the sum of page views \(PVs\). The aggregate view is refreshed based on the event time at the maximum intervals of 600 seconds. The data in the aggregate view is retained for 2,592,000 seconds and will be cleared after expiration.

-   Create a table and insert the sample data.

    ```
    CREATE TABLE agg_src_table
    (
        event_id INT
        ,user_id TEXT
        ,uid INT
        ,event_time timestamptz NOT NULL
        ,province TEXT
        ,city TEXT
        ,pv INT
        ,charge NUMERIC(15,2)
        ,event_count INT
    );
    
    insert into agg_src_table values 
    (1002, 'User10011', 10011, '2020-11-11 00:01:11', 'hebei', 'tangshan', 1, 10,10 ),
    (1002, 'User10012', 10012, '2020-11-11 00:01:12', 'shandong', 'qingdao', 2, 11,11 ),
    (1003, 'User10013', 10013, '2020-11-11 00:01:13', 'hunan', 'changsha', 2, 11.9, 11 ),
    (1002, 'User10012', 10012, '2020-11-11 00:01:14', 'shandong', 'qingdao', 1, 1.1, 9 ),
    (1002, 'User10012', 10012, '2020-11-11 00:01:14', 'shandong', 'qingdao', 1, 1.1, 9 );
                            
    ```

-   Create a view for the table to pre-aggregate the data and truncate the values of the event\_time field to be accurate to the hour.

    ```
    CREATE view agg_src_view AS
    SELECT  province
            ,city
            ,event_time
            ,date_trunc('hour', event_time) AS event_date
            ,pv
            ,charge
            ,event_count
            ,user_id
            ,uid
    FROM    agg_src_table ;
                            
    ```

-   Create an aggregate view.

    **Note:** To accurately remove duplicate values for user IDs, you can use the rb\_count\_distinct function. Before that, you must install an extension for roaring bitmaps. For more information, see [t2069643.md\#]().

    ```
    -- Install an extension for aggregate views.
    create extension  aggregate_view;
    -- Install an extension for roaring bitmaps.
    create extension  roaringbitmap;
    
    -- Create an aggregate view. In this example, data is aggregated based on the province and city fields and the time specified by the event_date field. The count function is used for the event_count field, the avg function is used for the charge field, the sum function is used for the pv field, the approx_count_distinct function is used for the user_id field, and the rb_count_distinct function is used for the uid field. The aggregate view is refreshed based on the accurate time specified by the event_time field at the maximum intervals of 600 seconds. The data in the aggregate view is retained for 2,592,000 seconds.
    call hg_aggregate_view_create(
      'agg_dst_view', 
      'agg_src_view', 
      ARRAY['province', 'city'], 
      'event_date', 
      ARRAY['event_count', 'charge', 'pv', 'user_id', 'uid'], 
      ARRAY['count','avg', 'sum', 'approx_count_distinct', 'rb_count_distinct'],
      'event_time', 
      '600 sec', 
      '2592000');
    ```

-   Query data and insert data.

    ```
    -- Query business data.
    SELECT  province
            ,city
            ,event_date
            ,event_count
            ,charge
            ,pv
            ,approx_count_distinct_final(user_id) AS user_id
            ,rb_cardinality(uid) AS uid
    FROM    agg_dst_view
    WHERE   event_date > '2020-11-01'::timestamptz
    AND     event_date < '2020-11-12'::timestamptz;
    
    -- Insert data into the source table.
    insert into agg_src_table values 
    (1002, 'User10998', 10320, '2020-11-11 00:01:15', 'sichuan', 'chengdu', 2, 3,1 ),
    (1003, 'User10007', 10343, '2020-11-12 00:01:15', 'zhejiang', 'hangzhou', 7, 2,4 ),
    (1003, 'User10073', 10221, '2020-11-12 00:01:16', 'anhui', 'huangshan', 3, 8,4 );
    
    -- Refresh the aggregate view at the maximum refresh interval. For example, the current time is 2020-11-12 00:01:16. Then, the data that is written from 2020-11-12 00:01:16 to 2020-11-11 23:51:16 is updated.
    call hg_aggregate_view_refresh('agg_dst_view');
    
    -- Update the data that is written before 2020-11-12 01:20:00.
    call hg_aggregate_view_refresh('agg_dst_view', '2020-11-12 01:20:00'::timestamptz,'true');
    
    -- Approximately remove duplicate values for user IDs and use the return value to query data.
    SELECT  city
            ,approx_count_distinct_final(approx_count_distinct_merge(user_id)) AS user_id
    FROM    agg_dst_view
    WHERE   event_date > '2020-11-11'::timestamptz
    AND     event_date < '2020-11-13'::timestamptz
    GROUP BY city;
    
    -- Accurately remove duplicate values for UIDs and use the return value to query data.
      SELECT  city
            ,SUM(pv)
            ,rb_cardinality(rb_or_agg(uid)) AS uid
    FROM    agg_dst_view
    WHERE   event_date > '2020-11-10'::timestamptz
    AND     event_date < '2020-11-12'::timestamptz
    GROUP BY city;
    
    -- Use the return values of aggregate functions to query data.
    SELECT  province
            ,SUM(event_count)
            ,SUM(pv)
            ,approx_count_distinct_final(approx_count_distinct_merge(user_id)) AS user_id
            ,rb_cardinality(rb_or_agg(uid)) AS uid
    FROM    agg_dst_view
    WHERE   event_date > '2020-11-10'::timestamptz
    AND     event_date < '2020-11-15'::timestamptz
    GROUP BY province;
    ```

-   Delete an aggregate view.

    ```
    -- Delete an aggregate view.
    call hg_aggregate_view_delete('agg_dst_view');
    ```


