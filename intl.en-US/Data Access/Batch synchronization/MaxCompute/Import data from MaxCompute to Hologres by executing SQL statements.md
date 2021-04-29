---
keyword: [SQL, Hologres, partitioned table, non-partitioned table]
---

# Import data from MaxCompute to Hologres by executing SQL statements

This topic shows you how to import data from MaxCompute to Hologres by executing SQL statements.

In some scenarios, you may have more than 200 GB of business data in MaxCompute and require second-level responses when you perform complex queries on the data. To meet this requirement, you can import the MaxCompute data to Hologres for queries.

## Usage notes

When you import data from MaxCompute to Hologres by executing SQL statements, take note of the following items:

-   Partition fields in MaxCompute tables can be mapped to partition fields or regular fields in Hologres tables. Therefore, you can import data from a partitioned MaxCompute table to a partitioned or non-partitioned Hologres table.
-   In a partitioned Hologres table, the partition key can include only one partition field. Assume that you import data from a partitioned MaxCompute table whose partition key includes multiple partition fields to a partitioned Hologres table. You only need to map one of the partition fields in the MaxCompute table to the partition key of the Hologres table. The other partition fields in the MaxCompute table are mapped to regular fields in the Hologres table.
-   To update and overwrite existing data when you import data, use an [INSERT ON CONFLICT](/intl.en-US/Hologres SQL/DML&DQL/INSERT ON CONFLICT.md) statement.
-   For information about data type mappings between MaxCompute and Hologres, see [Data types](/intl.en-US/Hologres SQL/Data types/Data types.md).

## Import data from a non-partitioned MaxCompute table to Hologres for queries

1.  Create a non-partitioned table in MaxCompute.

    Create a non-partitioned table in MaxCompute. You can also use an existing non-partitioned table in MaxCompute.

    In this example, the customer table in the MaxCompute public dataset named **public\_data** is used. You can log on to the MaxCompute console and query the dataset. For more information, see [t12301.md\#section\_9hk\_a70\_qsg](/intl.en-US/Public Datasets/Overview.md). The following code shows the data definition language \(DDL\) statement that is used to create the table and the SELECT statement that is used to query the table:

    ```
    --The DDL statement that is used to create the customer table.
    CREATE TABLE IF NOT EXISTS public_data.customer(
      c_customer_sk BIGINT,
      c_customer_id STRING,
      c_current_cdemo_sk BIGINT,
      c_current_hdemo_sk BIGINT,
      c_current_addr_sk BIGINT,
      c_first_shipto_date_sk BIGINT,
      c_first_sales_date_sk BIGINT,
      c_salutation STRING,
      c_first_name STRING,
      c_last_name STRING,
      c_preferred_cust_flag STRING,
      c_birth_day BIGINT,
      c_birth_month BIGINT,
      c_birth_year BIGINT,
      c_birth_country STRING,
      c_login STRING,
      c_email_address STRING,
      c_last_review_dateSTRING,
      useless STRING);
    
    --The SELECT statement that is used to query the customer table.
    SELECT * FROM public_data.customer;
    ```

    The following figure shows part of the data in the customer table.

    ![customer](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7887946161/p242401.png)

2.  Create a foreign table in Hologres.

    Create a foreign table in Hologres to map the source table in MaxCompute. For example, you can execute the following statement:

    ```
    CREATE FOREIGN TABLE foreign_customer (
        "c_customer_sk" int8,
        "c_customer_id" text,
        "c_current_cdemo_sk" int8,
        "c_current_hdemo_sk" int8,
        "c_current_addr_sk" int8,
        "c_first_shipto_date_sk" int8,
        "c_first_sales_date_sk" int8,
        "c_salutation" text,
        "c_first_name" text,
        "c_last_name" text,
        "c_preferred_cust_flag" text,
        "c_birth_day" int8,
        "c_birth_month" int8,
        "c_birth_year" int8,
        "c_birth_country" text,
        "c_login" text,
        "c_email_address" text,
        "c_last_review_date" text,
        "useless" text
    )
    SERVER odps_server
    OPTIONS (project_name 'public_data', table_name 'customer');
    ```

    |Parameter|Description|
    |---------|-----------|
    |Server|The server where the foreign table resides. You can use the **odps\_server** server that is created at the underlying layer of Hologres. For more information, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k).|
    |Project\_Name|The name of the project where the MaxCompute table to be queried resides.|
    |Table\_Name|The name of the MaxCompute table to be queried.|

    The data types of the fields in the foreign table must map those in the MaxCompute table. For more information about the mappings between data types, see [Data type mappings between MaxCompute and Hologres when you create a foreign table](/intl.en-US/Hologres SQL/Data types/Data types.mdsection_w14_cec_th7).

3.  Create a table in Hologres to store received data.

    Create a table in Hologres to store data received from the source MaxCompute table.

    The following sample statements are only for your reference. When you create your own table, set the schema based on business needs and create appropriate indexes to achieve better query performance. For more information about table properties, see [CREATE TABLE](/intl.en-US/Hologres SQL/DDL/TABLE/CREATE TABLE.md).

    ```
    --Create a column-oriented table to store received data.
    BEGIN;
    CREATE TABLE public.holo_customer (
     "c_customer_sk" int8,
     "c_customer_id" text,
     "c_current_cdemo_sk" int8,
     "c_current_hdemo_sk" int8,
     "c_current_addr_sk" int8,
     "c_first_shipto_date_sk" int8,
     "c_first_sales_date_sk" int8,
     "c_salutation" text,
     "c_first_name" text,
     "c_last_name" text,
     "c_preferred_cust_flag" text,
     "c_birth_day" int8,
     "c_birth_month" int8,
     "c_birth_year" int8,
     "c_birth_country" text,
     "c_login" text,
     "c_email_address" text,
     "c_last_review_date" text,
     "useless" text
    );
    CALL SET_TABLE_PROPERTY('public.holo_customer', 'orientation', 'column');
    CALL SET_TABLE_PROPERTY('public.holo_customer', 'bitmap_columns', 'c_customer_id,c_salutation,c_first_name,c_last_name,c_preferred_cust_flag,c_birth_country,c_login,c_email_address,c_last_review_date,useless');
    CALL SET_TABLE_PROPERTY('public.holo_customer', 'dictionary_encoding_columns', 'c_customer_id:auto,c_salutation:auto,c_first_name:auto,c_last_name:auto,c_preferred_cust_flag:auto,c_birth_country:auto,c_login:auto,c_email_address:auto,c_last_review_date:auto,useless:auto');
    CALL SET_TABLE_PROPERTY('public.holo_customer', 'time_to_live_in_seconds', '3153600000');
    CALL SET_TABLE_PROPERTY('public.holo_customer', 'storage_format', 'segment');
    COMMIT;
    ```

4.  Import data to Hologres.

    Execute an `INSERT` statement to import data from the source MaxCompute table to Hologres. You can import part of or all the fields. If you import part of the fields, make sure that the fields are arranged in the same order as the mapping fields in the destination Hologres table. For example, you can execute one of the following statements:

    ```
    --Import all the fields.
    INSERT INTO holo_customer
    SELECT * FROM foreign_customer;
    
    --Import part of the fields.
    INSERT INTO holo_customer
    SELECT 
        "c_customer_sk",
        "c_customer_id",
        "c_email_address",
        "c_last_review_date",
        "useless"
    FROM foreign_customer;
    ```

5.  Query the data imported from the source MaxCompute table in Hologres.

    Execute an SELECT statement to query the data that is imported from the source MaxCompute table to Hologres. For example, you can execute the following statement:

    ```
    SELECT * FROM holo_customer;
    ```


## Import data from a partitioned MaxCompute table to Hologres for queries

1.  Create a partitioned table in MaxCompute.

    For more information, see [Partition and column operations](/intl.en-US/Development/SQL/DDL SQL/Partition and column operations.md). You can also use an existing partitioned table in MaxCompute.

    In this example, the ods\_enterprise\_share\_trade\_h table in the MaxCompute public dataset named **public\_data** is used. You can log on to the MaxCompute console and query the dataset. For more information, see [Public datasets](/intl.en-US/Public Datasets/Overview.md). The following code shows the DDL statement that is used to create the table and the SELECT statement that is used to query a partition of the table:

    ```
    --The DDL statement that is used to create the ods_enterprise_share_trade_h table.
    CREATE TABLE IF NOT EXISTS public_data.ods_enterprise_share_trade_h(
      trde_date STRING COMMENT 'Date',
      open STRING COMMENT 'Opening price',
      high STRING COMMENT 'Highest price',
      close STRING COMMENT 'Closing price',
      low STRING COMMENT 'Lowest price',
      volume STRING COMMENT 'Trading volume',
      price_change STRING COMMENT 'Price change',
      p_change STRING COMMENT 'Price movement',
      ma5 STRING COMMENT 'Average price in the past five days',
      ma10 STRING COMMENT 'Average price in the past 10 days',
      ma20 STRING COMMENT 'Average price in the past 20 days',
      v_ma5 STRING COMMENT 'Average trading volume in the past five days',
      v_ma10 STRING COMMENT 'Average trading volume in the past 10 days',
      v_ma20 STRING COMMENT 'Average trading volume in the past 20 days',
      turnover STRING COMMENT 'Turnover',
      code STRING COMMENT 'Stock symbol') 
      PARTITIONED BY (ds STRING) STORED AS ALIORC TBLPROPERTIES ('comment'='Stock transaction history');
      
      --The SELECT statement that is used to query a partition of the ods_enterprise_share_trade_h table.
      SELECT * FROM public_data.ods_enterprise_share_trade_h WHERE ds = '20170113';
    ```

    The following figure shows part of the data in the ods\_enterprise\_share\_trade\_h table.

    ![trade](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7887946161/p242410.png)

2.  Create a foreign table in Hologres.

    Create a foreign table in Hologres to map the source table in MaxCompute. For example, you can execute the following statements:

    ```
    CREATE FOREIGN TABLE public.foreign_ods_enterprise_share_trade_h (
        "trde_date" text,
        "open" text,
        "high" text,
        "close" text,
        "low" text,
        "volume" text,
        "price_change" text,
        "p_change" text,
        "ma5" text,
        "ma10" text,
        "ma20" text,
        "v_ma5" text,
        "v_ma10" text,
        "v_ma20" text,
        "turnover" text,
        "code" text,
        "ds" text
    )
    SERVER odps_server
    OPTIONS (project_name 'public_data', table_name 'ods_enterprise_share_trade_h');
    comment on foreign table public.foreign_ods_enterprise_share_trade_h is 'Stock transaction history';
    comment on column public.foreign_ods_enterprise_share_trade_h."trde_date" is 'Date';
    comment on column public.foreign_ods_enterprise_share_trade_h."open" is 'Opening price';
    comment on column public.foreign_ods_enterprise_share_trade_h."high" is 'Highest price';
    comment on column public.foreign_ods_enterprise_share_trade_h."close" is 'Closing price';
    comment on column public.foreign_ods_enterprise_share_trade_h."low" is 'Lowest price';
    comment on column public.foreign_ods_enterprise_share_trade_h."volume" is 'Trading volume';
    comment on column public.foreign_ods_enterprise_share_trade_h."price_change" is 'Price change';
    comment on column public.foreign_ods_enterprise_share_trade_h."p_change" is 'Price movement';
    comment on column public.foreign_ods_enterprise_share_trade_h."ma5" is 'Average price in the past five days';
    comment on column public.foreign_ods_enterprise_share_trade_h."ma10" is 'Average price in the past 10 days';
    comment on column public.foreign_ods_enterprise_share_trade_h."ma20" is 'Average price in the past 20 days';
    comment on column public.foreign_ods_enterprise_share_trade_h."v_ma5" is 'Average trading volume in the past five days';
    comment on column public.foreign_ods_enterprise_share_trade_h."v_ma10" is 'Average trading volume in the past 10 days';
    comment on column public.foreign_ods_enterprise_share_trade_h."v_ma20" is 'Average trading volume in the past 20 days';
    comment on column public.foreign_ods_enterprise_share_trade_h."turnover" is 'Turnover';
    comment on column public.foreign_ods_enterprise_share_trade_h."code" is 'Stock symbol';
    ```

3.  Create a table in Hologres to store received data.

    Create a partitioned table in Hologres to store data received from the source MaxCompute table. For example, you can execute the following statements to create a parent partitioned table.

    The following sample statements are only for your reference. When you create your own table, set the schema based on business needs and create appropriate indexes to achieve better query performance. For more information about table properties, see [CREATE TABLE](/intl.en-US/Hologres SQL/DDL/TABLE/CREATE TABLE.md).

    ```
    BEGIN;
    CREATE TABLE public.holo_ods_enterprise_share_trade_h (
     "trde_date" text,
     "open" text,
     "high" text,
     "close" text,
     "low" text,
     "volume" text,
     "price_change" text,
     "p_change" text,
     "ma5" text,
     "ma10" text,
     "ma20" text,
     "v_ma5" text,
     "v_ma10" text,
     "v_ma20" text,
     "turnover" text,
     "code" text,
     "ds" text
    )
    PARTITION BY LIST (ds);
    CALL SET_TABLE_PROPERTY('public.holo_ods_enterprise_share_trade_h', 'orientation', 'column');
    CALL SET_TABLE_PROPERTY('public.holo_ods_enterprise_share_trade_h', 'bitmap_columns', 'trde_date,open,high,close,low,volume,price_change,p_change,ma5,ma10,ma20,v_ma5,v_ma10,v_ma20,turnover,code,ds');
    CALL SET_TABLE_PROPERTY('public.holo_ods_enterprise_share_trade_h', 'dictionary_encoding_columns', 'trde_date:auto,open:auto,high:auto,close:auto,low:auto,volume:auto,price_change:auto,p_change:auto,ma5:auto,ma10:auto,ma20:auto,v_ma5:auto,v_ma10:auto,v_ma20:auto,turnover:auto,code:auto,ds:auto');
    CALL SET_TABLE_PROPERTY('public.holo_ods_enterprise_share_trade_h', 'time_to_live_in_seconds', '3153600000');
    CALL SET_TABLE_PROPERTY('public.holo_ods_enterprise_share_trade_h', 'storage_format', 'segment');
    comment on column public.holo_ods_enterprise_share_trade_h.trde_date is 'Date';
    comment on column public.holo_ods_enterprise_share_trade_h.open is 'Opening price';
    comment on column public.holo_ods_enterprise_share_trade_h.high is 'Highest price';
    comment on column public.holo_ods_enterprise_share_trade_h.close is 'Closing price';
    comment on column public.holo_ods_enterprise_share_trade_h.low is 'Lowest price';
    comment on column public.holo_ods_enterprise_share_trade_h.volume is 'Trading volume';
    comment on column public.holo_ods_enterprise_share_trade_h.price_change is 'Price change';
    comment on column public.holo_ods_enterprise_share_trade_h.p_change is 'Price movement';
    comment on column public.holo_ods_enterprise_share_trade_h.ma5 is 'Average price in the past five days';
    comment on column public.holo_ods_enterprise_share_trade_h.ma10 is 'Average price in the past 10 days';
    comment on column public.holo_ods_enterprise_share_trade_h.ma20 is 'Average price in the past 20 days';
    comment on column public.holo_ods_enterprise_share_trade_h.v_ma5 is 'Average trading volume in the past five days';
    comment on column public.holo_ods_enterprise_share_trade_h.v_ma10 is 'Average trading volume in the past 10 days';
    comment on column public.holo_ods_enterprise_share_trade_h.v_ma20 is 'Average trading volume in the past 20 days';
    comment on column public.holo_ods_enterprise_share_trade_h.turnover is 'Turnover';
    comment on column public.holo_ods_enterprise_share_trade_h.code is 'Stock symbol';
    COMMIT;
    ```

    **Note:** In a partitioned Hologres table, the partition key can include only one partition field. You can import data from a partitioned MaxCompute table whose partition key includes multiple partition fields to a non-partitioned Hologres table or a partitioned Hologres table whose partition key includes only one partition field.

4.  Create child partitioned tables.

    After you create the parent partitioned table, you must also create child partitioned tables. This way, data in the partitions of the MaxCompute table can be imported to the corresponding child partitioned tables in Hologres. For example, you can execute the following statements to create child partitioned tables.

    Data of each partition of the MaxCompute table is imported to the corresponding child partitioned table in Hologres. If the MaxCompute table is partitioned by date, we recommend that you add each date to the name of the corresponding child partitioned table for easier identification.

    ```
    --Create a child partitioned table that corresponds to the 20170113 partition.
    CREATE TABLE IF NOT EXISTS "public".holo_ods_enterprise_share_trade_h_20170113 PARTITION OF "public".holo_ods_enterprise_share_trade_h FOR VALUES IN ('20170113');
    
    --Create a child partitioned table that corresponds to the 20170114 partition.
    CREATE TABLE IF NOT EXISTS "public".holo_ods_enterprise_share_trade_h_20170114 PARTITION OF "public".holo_ods_enterprise_share_trade_h FOR VALUES IN ('20170114');
    ```

5.  Import data to Hologres.

    You cannot import data from the partitioned MaxCompute table to the parent partitioned table in Hologres. You can import the data only to the child partitioned tables. Make sure that the data of each MaxCompute partition is imported to the child partitioned table that has the same partition key value. For example, you can execute the following statements to import data:

    ```
    --Import the data of the 20170113 partition to the corresponding child partitioned table.
    INSERT INTO "public".holo_ods_enterprise_share_trade_h_20170113
    SELECT *
    FROM "public".foreign_ods_enterprise_share_trade_h
    WHERE ds='20170113';
    
    --Import the data of the 20170114 partition to the corresponding child partitioned table.
    INSERT INTO "public".holo_ods_enterprise_share_trade_h_20170114
    SELECT *
    FROM "public".foreign_ods_enterprise_share_trade_h
    WHERE ds='20170114';
    ```

6.  Query the data imported from the MaxCompute table in Hologres.

    After you import the data, you can query the data in Hologres. You can query the parent partitioned table or the child partitioned tables. For example, you can execute the following statements:

    ```
    --Query the parent partitioned table.
    SELECT COUNT (*) FROM holo_ods_enterprise_share_trade_h;
    
    --Query a child partitioned table.
    SELECT trde_date FROM holo_ods_enterprise_share_trade_h_20170113;
    ```


## Synchronize data by using a visualization tool or by periodic scheduling

You can synchronize a large amount of data at a time to Hologres by using a visualization tool or by periodic scheduling.

-   HoloWeb: You can use HoloWeb to synchronize MaxCompute data to Hologres without the need to execute SQL statements. However, HoloWeb does not support scheduling. For more information about how to use HoloWeb, see [Synchronize MaxCompute data with a few clicks](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/MaxCompute Acceleration/Synchronize MaxCompute data with a few clicks.md).

