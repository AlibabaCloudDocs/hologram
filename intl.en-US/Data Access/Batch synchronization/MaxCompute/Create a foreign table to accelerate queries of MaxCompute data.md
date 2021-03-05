---
keyword: [accelerated queries of MaxCompute data, Hologres, quick queries of MaxCompute data]
---

# Create a foreign table to accelerate queries of MaxCompute data

This topic shows you how to create a foreign table in Hologres to achieve accelerated queries of MaxCompute data.

MaxCompute is a fast and fully managed computing platform for large-scale data warehousing. It can process exabytes of data. MaxCompute is used to store and compute large amounts of structured data at a time. It provides various data warehousing solutions as well as big data analytics and modeling services.

Hologres is a real-time interactive analytics data warehouse that is compatible with PostgreSQL. Hologres seamlessly integrates with MaxCompute at the underlying layer. Hologres allows you to perform accelerated queries of MaxCompute data by creating foreign tables, without the need to store data in Hologres or import data to and export data from Hologres.

You can also query data that has been imported to Hologres. Compared with other similar services that are not oriented for the big data ecosystem, Hologres offers higher performance and faster data import and export.

You can select one of the following query methods based on the business features and scenarios:

-   Directly query MaxCompute data:

    This method is suitable for scenarios where the volume of data to be queried at a time is less than 100 GB.

    **Note:** The volume of data to be queried indicates the volume of data in the partitions that hit the filter conditions, but not the size of queried fields.

-   Query MaxCompute data that has been imported to Hologres:

    This method is suitable for the following scenarios: the size of a single table to be queried being larger than 100 GB, complex queries, index-based queries, and queries that involve UPDATE and INSERT operations.


## Query data in a MaxCompute non-partitioned table

1.  Create a non-partitioned table in MaxCompute.

    Create a non-partitioned table in MaxCompute and import data to the table. For more information, see [Create and view a table](/intl.en-US/Quick Start/Create and view a table.md). You can also use an existing MaxCompute non-partitioned table.

    In this example, an existing MaxCompute non-partitioned table is used. You can also execute the following statements to create a non-partitioned table in MaxCompute and import data to the table:

    ```
    CREATE TABLE weather (
        city            STRING ,
        temp_lo         int,           // The lowest temperature.
        temp_hi         int           // The highest temperature.
    );
    INSERT INTO weather VALUES 
    ('beijing',40,50),
    ('hangzhou',46,55);
    ```

2.  Create a foreign table in Hologres.

    Create a foreign table in Hologres to map the source table in MaxCompute. You can configure the foreign table to include part of or all fields in the MaxCompute table. For example, you can execute the following statement to create a foreign table:

    ```
    CREATE FOREIGN TABLE weather1 (
     city text,
     temp_lo int8,
     temp_hi int8
    )
    SERVER odps_server
    OPTIONS (project_name '<projectname>',table_name 'weather');
    ```

    The following table describes the parameters in the statement.

    |Parameter|Description|
    |---------|-----------|
    |SERVER|The server on which the foreign table is stored.You can select the **odps\_server** server that is created at the underlying layer of Hologres. For more information, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k). |
    |project\_name|The name of the project where the MaxCompute table to be queried resides.|
    |table\_name|The name of the MaxCompute table to be queried.|

    **Note:** :

    -   The data types of the fields in the foreign table must map those in the MaxCompute table. For more information about the mappings between data types, see [Data type mappings between MaxCompute and Hologres when you create a foreign table](/intl.en-US/Hologres SQL/Data types/Data types.md).
    -   Hologres allows you to execute the `IMPORT FOREIGN SCHEMA` statement to create multiple foreign tables at a time. For more information, see [IMPORT FOREIGN SCHEMA](/intl.en-US/Hologres SQL/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md). You can also create a node in the **Data Analytics** module of HoloStudio to execute the statement as scheduled. This way, the foreign table in Hologres can be synchronously updated when the MaxCompute table is updated. For more information, see [Hologres Data Analytics]().
    -   You can use Hologres to accelerate queries of only internal tables of MaxCompute. Queries of foreign tables and views of MaxCompute cannot be accelerated.
3.  Query data of the non-partitioned table by using the foreign table.

    After you create a foreign table, you can directly query the foreign table for data in the mapping MaxCompute table. For example, you can execute the following statement to query the foreign table:

    ```
    SELECT * FROM weather1;
    ```


## Query data in a MaxCompute partitioned table

1.  Create a partitioned table in MaxCompute.

    Create a partitioned table in MaxCompute and import data to the table. For more information, see [Partition and column operations](/intl.en-US/Development/SQL/DDL SQL/Partition and column operations.md). You can also use an existing MaxCompute partitioned table.

    In this example, an existing MaxCompute partitioned table is used. You can also execute the following statements to create a partitioned table in MaxCompute and import data to the table:

    ```
    CREATE TABLE odps_test
    (
        shop_name     string,
        customer_id   string,
        total_price   INT 
    )
    PARTITIONED BY  (sale_date string);
    INSERT  OVERWRITE TABLE odps_test PARTITION  (sale_date='2013') VALUES ('shop', '1234', 12);
    INSERT  OVERWRITE TABLE odps_test PARTITION  (sale_date='2014')VALUES ('rest', '1111', 13);
    INSERT  OVERWRITE TABLE odps_test PARTITION (sale_date='2015')VALUES ('texy', '2222', 14);
    ```

2.  Create a foreign table in Hologres.

    Create a foreign table in Hologres to map the source table in MaxCompute. For example, you can execute the following statement to create a foreign table:

    ```
    CREATE FOREIGN TABLE table_odps (
     shop_name text,
     customer_id text,
     total_price int8,
     sale_date text
    )
    SERVER odps_server
    OPTIONS (project_name '<projectname>', table_name 'odps_test');
    ```

    **Note:** A foreign table in Hologres is used to map fields in the source table, but is not used to store data. A partition field of the source table in MaxCompute maps a common field of the foreign table in Hologres.

3.  Query data of the partitioned table by using the foreign table.

    Execute the following statement to query all data in the partitioned table:

    ```
    SELECT * FROM table_odps;
    ```

    Execute the following statement to query data of a specified partition:

    ```
    SELECT * FROM table_odps 
    WHERE sale_date = '2013';
    ```


