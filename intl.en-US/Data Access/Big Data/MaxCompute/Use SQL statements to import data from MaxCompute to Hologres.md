---
keyword: [SQL, Hologres, partitioned table, non-partitioned table]
---

# Use SQL statements to import data from MaxCompute to Hologres

This topic describes how to use SQL statements to import data from MaxCompute to Hologres.

In specific scenarios, you may require quick responses to complex queries in seconds from a large amount of data greater than 100 GB in MaxCompute. To meet this requirement, you can use Hologres to import data from MaxCompute to Hologres for queries.

## Import data from a MaxCompute non-partitioned table to Hologres for queries

1.  Create a non-partitioned table in MaxCompute.

    Create a non-partitioned source table in MaxCompute, or use an existing non-partitioned table in MaxCompute.

    In this example, an existing non-partitioned table named **odps\_test1** in Data Map is used. You can use the following sample SQL statements to create a table and insert data:

    ```
    CREATE TABLE odps_test1(
        id int,
        name STRING ,
        class STRING 
    );
    INSERT INTO odps_test1 VALUES 
    (1,'Tom','class3'),
    (2,'Neal','class4'),
    (3,'Tony','class4'),
    (4,'David','class5');
    ```

2.  Create a foreign table in Hologres.

    Create a foreign table in Hologres to map the source table in MaxCompute. For example, you can use the following SQL statement to create a foreign table:

    ```
    CREATE FOREIGN TABLE holo_test1 (
     id int8,
     name text,
     class text
    )
    SERVER odps_server
    OPTIONS (project_name '<odpsprojectname>', table_name 'odps_test1');
    ```

    |Parameter|Description|
    |---------|-----------|
    |Server|The foreign server that stores the connection information for accessing the MaxCompute table to be queried. You can directly call the **odps\_server** server created at the underlying layer of Hologres. For more information, see [Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k).|
    |Project\_Name|The name of the project where the MaxCompute table to be queried resides.|
    |Table\_Name|The name of the MaxCompute table to be queried.|

    The field types of the foreign table must map those of the MaxCompute table. For more information about data type mappings, see [Data type mappings between MaxCompute and Hologres when you create a foreign MaxCompute table](/intl.en-US/Hologres SQL/Data types/Data types.md).

3.  Create a table in Hologres to store the received data.

    Create a table in Hologres to receive data from the MaxCompute source table.

    The field types of this table must map those of the MaxCompute source table.

    For example, you can use the following SQL statement to create a table to store the received data:

    ```
    CREATE TABLE holo_test2 (
     id int8,
     name text,
     class text
    );
    ```

4.  Import data to Hologres.

    Use the `INSERT` statement to import data from MaxCompute to Hologres. You can execute an SQL statement similar to the following example:

    ```
    INSERT INTO holo_test2
    SELECT 
    id as id,
    name as name,
    class as class
    FROM holo_test1;
    ```

5.  Query the imported data.

    Use the SELECT statement to query data imported from the MaxCompute source table to Hologres. For example, you can use the following SQL statement to query the imported data:

    ```
    SELECT * FROM holo_test2;
    ```


## Import data from a MaxCompute partitioned data to Hologres for queries

1.  Create a partitioned table in MaxCompute.

    For more information about how to create a partitioned table in MaxCompute, see Partition and column operations. Alternatively, use an existing partitioned table in MaxCompute.

    In this example, an existing partitioned table named **odps\_test2** in Data Map is used. You can use the following sample SQL statements to create a table and insert data:

    ```
     CREATE TABLE odps_test2
    (
        shop_name     string,
        customer_id   string,
        total_price   INT 
    )
    PARTITIONED BY (sale_date string);
    INSERT  overwrite table odps_test2 partition (sale_date='2013')values ('shop', '1234', 12);
    INSERT  overwrite table odps_test2 partition (sale_date='2014')values ('rest', '1111', 13);
    INSERT  overwrite table odps_test2 partition (sale_date='2015')values ('texy', '2222', 14);
    ```

2.  Create a foreign table in Hologres.

    Create a foreign table in Hologres to map the source table in MaxCompute. For example, you can use the following SQL statement to create a foreign table:

    ```
    CREATE FOREIGN TABLE table1_odps (
     shop_name text,
     customer_id text,
     total_price int8,
     sale_date text
    )
    SERVER odps_server
    OPTIONS (project_name '<odpsprojectname>', table_name 'odps_test2');
    ```

3.  Create a table in Hologres to store the received data.

    Create a table in Hologres to receive data from the MaxCompute source table.

    Data import from MaxCompute to Hologres succeeds regardless of whether data in Hologres is partitioned. You can import data from the MaxCompute partitioned table to a partitioned or non-partitioned table in Hologres.

    In this example, data is imported to a partitioned table in Hologres. For example, you can use the following SQL statement to create a partitioned table:

    ```
    CREATE TABLE table1_holo (
     shop_name text,
     customer_id text,
     total_price int8,
     sale_date text
    )
    PARTITION BY LIST (sale_date);
    ```

    **Note:** Hologres does not support multi-level partitioning. Therefore, you can import data in a multi-level MaxCompute partitioned table to a non-partitioned table or a partitioned table with only one partition level in Hologres.

4.  Create a child partitioned table.

    Create a child partitioned table in Hologres to receive data from the specified partition. For example, you can use the following SQL statement to create a child partitioned table:

    ```
    CREATE TABLE  table1_holo_0001
    PARTITION  of table1_holo
    for VALUES  in ('2015');
    ```

5.  Import data to Hologres.

    Use the `INSERT` statement to import data from the MaxCompute partitioned table to the specified child partitioned table in Hologres. You can execute an SQL statement similar to the following example:

    ```
    INSERT INTO table1_holo_0001
    SELECT
     shop_name as shop_name,
     customer_id as customer_id,
     total_price as total_price,
     sale_date as sale_date
     from table1_odps
     WHERE sale_date = '2015';
    ```

6.  Query the imported data.

    Use the SELECT statement to query data imported from the MaxCompute source table to Hologres. For example, you can use the following SQL statement to query the imported table:

    ```
    SELECT * FROM table1_holo;
    ```


