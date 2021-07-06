---
keyword: [TPC-H, OLAP queries, point queries of key-value pairs]
---

# Performance testing based on TPC-H

This topic describes how to use TPC Benchmark H \(TPC-H\) to test the performance of Online Analytical Processing \(OLAP\) queries and point queries of key-value pairs.

## About TPC-H

TPC-H is a test set developed by the Transaction Processing Performance Council \(TPC\) to simulate decision support systems. TPC-H is used in academia and industries to evaluate the performance of decision support systems. TPC-H models data in production environments to simulate the data warehouse of a sales system. The data warehouse consists of eight tables. The size of each table ranges from 1 GB to 3 TB. The TPC-H benchmark test involves 22 query statements. The test focuses on the response time of each query, that is, the time required from submitting the query to receiving the return result. The test results can comprehensively reflect the capability of the system to process queries.

The following figure shows the relationships between the eight tables involved in the TPC-H benchmark test.

![TPC-H tables](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0222945261/p276682.png)

## Scenarios

In this topic, the following two tests are performed:

-   [Performance test on OLAP queries](#section_faj_zuf_r1h): Create eight column-oriented tables, import data to the tables, and then execute the 22 query statements of the TPC-H benchmark test.
-   [Performance test on point queries of key-value pairs](#section_g4g_vro_v6x): Create a row-oriented table based on the schema defined for the ORDERS table and execute point queries based on the primary key.

The test data volume affects the test results. The data generation tool provided by TPC-H allows you to change the scale factor \(SF\) to adjust the volume of the data to be generated. One SF indicates 1 GB of data.

**Note:** The above-mentioned data volume refers to the volume of **raw data**. When you prepare the test environment, you must also consider other factors such as the space to be occupied by indexes.

## Preparations

You must prepare the basic environment and TPC-H data required for the tests on OLAP queries and point queries of key-value pairs. The following tables describe the steps to be performed to prepare the basic environment and TPC-H data.

-   **Basic environment preparation**

    |Step|Description|
    |----|-----------|
    |1.Create an ECS instance|Log on to the Alibaba Cloud Management Console and create an Elastic Compute Service \(ECS\) instance. The ECS instance is used to generate data, import data to Hologres, and test clients. We recommend that you create an ECS instance that meets the following conditions:    -   Instance type: ecs.g6.4xlarge.
    -   Operating system: CentOS 7.9.
    -   ESSD: Determine the capacity based on the volume of the data to be tested.
    -   Region: We recommend that you deploy the ECS instance and Hologres instance in the same region and virtual private cloud \(VPC\).
For more information about how to create an ECS instance, see [Create an instance](/intl.en-US/Instance/Create an instance/Creation method overview.md).|
    |2.Create a Hologres instance|Create an exclusive Hologres instance that uses the pay-as-you-go billing method. This instance is used only for testing. You need to configure only 8 CPU cores and 32 GB memory for the instance. You can also configure the computing resources based on your business requirements.

Log on to the [Hologres console](https://hologram.console.aliyun.com/#/overview). On the Instances page, click **Create Instance**.

For more information about how to create a Hologres instance, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md). |
    |3.Create a test database|Connect to the created Hologres instance and create a database. In this example, a database named **tpch\_1sf** is created. For more information about how to create a database, see [Create a database](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a database.md). |

-   **TPC-H data generation**

    You must connect to the ECS instance from a remote client, download the data generation tool, compile the data generation code, and then generate the TPC-H data. The following table describes the steps to be performed to prepare the TPC-H data.

    |Step|Description|
    |----|-----------|
    |1.Prepare the data generation code|To prepare the data generation code, perform the following steps:    1.  Connect to the ECS instance.

For more information about how to connect to an ECS instance, see [Overview](/intl.en-US/Instance/Connect to instances/Overview.md).

    2.  After you connect to the ECS instance, run the `yum update` command to update all repositories.
    3.  Run the `yum install git` command to install Git.
    4.  Run the `yum install gcc` command to install GCC.
    5.  Run the `git clone https://github.com/gregrahn/tpch-kit.git` command to download the TPC-H data generation tool.
    6.  Run the `cd tpch-kit/dbgen` command to go to the directory of the data generation tool.
    7.  Run the `make` command to compile the data generation code. |
    |2.Generate data|After the code is compiled, you can run the `./dbgen --help` command to view the parameters provided by the data generation tool. The following examples show you how to generate TPC-H data for tests and view the generated files:    -   In this example, 1 GB of data is required. In this case, you can run the following command to generate data:

```
./dbgen -vf -s 1
```

If you need to generate more data, you can set the SF to a greater value. For example, you can run the following command to generate 1 TB of data:

```
./dbgen -vf -s 1000
```

In general, 32 CUs are sufficient to process TPC-H data whose SF is 10 and 256 CUs are sufficient to process TPC-H data whose SF is 50.

    -   The generated data is stored in ECS. You can run the following command to view the generated files. Eight files are generated. Each file contains the data for a table involved in tests.

        ```
ls | grep '.*.tbl'
        ``` |


## Performance test on OLAP queries

Before you test the performance of OLAP queries, you must create tables and import data to the tables. To improve query performance, you can collect the characteristics of each table after data is imported. Then, you can use pgbench to run the test. To run a performance test on OLAP queries, perform the following steps:

1.  **Create tables**

    1.  Run the following command on the ECS instance to install the PostgreSQL client, which is used to import data:

        ```
        yum install postgresql-server
        ```

    2.  After the PostgreSQL client is installed, run the following command to connect to the Hologres instance. For more information about the parameters in the command, see [Connect to a Hologres instance from the PostgreSQL client](/intl.en-US/Common Development Tools/Connect to a Hologres instance from the PostgreSQL client.md).

        ```
        PGUSER=<AccessID> PGPASSWORD=<AccessKey> psql -p <Port> -h <Endpoint> -d <Database>
        ```

    3.  After you connect to the Hologres instance from the PostgreSQL client, execute the following statements to create the tables required for the test:

        ```
        DROP TABLE IF EXISTS LINEITEM;
        
        BEGIN;
        CREATE TABLE LINEITEM
        (
            L_ORDERKEY      INT         NOT NULL,
            L_PARTKEY       INT         NOT NULL,
            L_SUPPKEY       INT         NOT NULL,
            L_LINENUMBER    INT         NOT NULL,
            L_QUANTITY      DECIMAL(15,2) NOT NULL,
            L_EXTENDEDPRICE DECIMAL(15,2) NOT NULL,
            L_DISCOUNT      DECIMAL(15,2) NOT NULL,
            L_TAX           DECIMAL(15,2) NOT NULL,
            L_RETURNFLAG    TEXT        NOT NULL,
            L_LINESTATUS    TEXT        NOT NULL,
            L_SHIPDATE      TIMESTAMPTZ NOT NULL,
            L_COMMITDATE    TIMESTAMPTZ NOT NULL,
            L_RECEIPTDATE   TIMESTAMPTZ NOT NULL,
            L_SHIPINSTRUCT  TEXT        NOT NULL,
            L_SHIPMODE      TEXT        NOT NULL,
            L_COMMENT       TEXT        NOT NULL,
            PRIMARY KEY (L_ORDERKEY,L_LINENUMBER)
        );
        CALL set_table_property('LINEITEM', 'clustering_key', 'L_SHIPDATE,L_ORDERKEY');
        CALL set_table_property('LINEITEM', 'segment_key', 'L_SHIPDATE');
        CALL set_table_property('LINEITEM', 'distribution_key', 'L_ORDERKEY');
        CALL set_table_property('LINEITEM', 'bitmap_columns', 'L_ORDERKEY,L_PARTKEY,L_SUPPKEY,L_LINENUMBER,L_RETURNFLAG,L_LINESTATUS,L_SHIPINSTRUCT,L_SHIPMODE,L_COMMENT');
        CALL set_table_property('LINEITEM', 'dictionary_encoding_columns', 'L_RETURNFLAG,L_LINESTATUS,L_SHIPINSTRUCT,L_SHIPMODE,L_COMMENT');
        CALL set_table_property('LINEITEM', 'time_to_live_in_seconds', '31536000');
        COMMIT;
        
        DROP TABLE IF EXISTS ORDERS;
        
        BEGIN;
        CREATE TABLE ORDERS
        (
            O_ORDERKEY      INT         NOT NULL PRIMARY KEY,
            O_CUSTKEY       INT         NOT NULL,
            O_ORDERSTATUS   TEXT        NOT NULL,
            O_TOTALPRICE    DECIMAL(15,2) NOT NULL,
            O_ORDERDATE     timestamptz NOT NULL,
            O_ORDERPRIORITY TEXT        NOT NULL,
            O_CLERK         TEXT        NOT NULL,
            O_SHIPPRIORITY  INT         NOT NULL,
            O_COMMENT       TEXT        NOT NULL
        );
        CALL set_table_property('ORDERS', 'segment_key', 'O_ORDERDATE');
        CALL set_table_property('ORDERS', 'distribution_key', 'O_ORDERKEY');
        CALL set_table_property('ORDERS', 'colocate_with', 'LINEITEM');
        CALL set_table_property('ORDERS', 'bitmap_columns', 'O_ORDERKEY,O_CUSTKEY,O_ORDERSTATUS,O_ORDERPRIORITY,O_CLERK,O_SHIPPRIORITY,O_COMMENT');
        CALL set_table_property('ORDERS', 'dictionary_encoding_columns', 'O_ORDERSTATUS,O_ORDERPRIORITY,O_CLERK,O_COMMENT');
        CALL set_table_property('ORDERS', 'time_to_live_in_seconds', '31536000');
        COMMIT;
        
        DROP TABLE IF EXISTS PARTSUPP;
        
        BEGIN;
        CREATE TABLE PARTSUPP
        (
            PS_PARTKEY    INT    NOT NULL,
            PS_SUPPKEY    INT    NOT NULL,
            PS_AVAILQTY   INT    NOT NULL,
            PS_SUPPLYCOST DECIMAL(15,2) NOT NULL,
            PS_COMMENT    TEXT   NOT NULL,
            PRIMARY KEY(PS_PARTKEY,PS_SUPPKEY)
        );
        CALL set_table_property('PARTSUPP', 'distribution_key', 'PS_PARTKEY');
        CALL set_table_property('PARTSUPP', 'colocate_with', 'LINEITEM');
        CALL set_table_property('PARTSUPP', 'bitmap_columns', 'PS_PARTKEY,PS_SUPPKEY,PS_AVAILQTY,PS_COMMENT');
        CALL set_table_property('PARTSUPP', 'dictionary_encoding_columns', 'PS_COMMENT');
        CALL set_table_property('PARTSUPP', 'time_to_live_in_seconds', '31536000');
        COMMIT;
        
        DROP TABLE IF EXISTS PART;
        
        BEGIN;
        CREATE TABLE PART
        (
            P_PARTKEY     INT    NOT NULL PRIMARY KEY,
            P_NAME        TEXT   NOT NULL,
            P_MFGR        TEXT   NOT NULL,
            P_BRAND       TEXT   NOT NULL,
            P_TYPE        TEXT   NOT NULL,
            P_SIZE        INT    NOT NULL,
            P_CONTAINER   TEXT   NOT NULL,
            P_RETAILPRICE DECIMAL(15,2) NOT NULL,
            P_COMMENT     TEXT   NOT NULL
        );
        CALL set_table_property('PART', 'distribution_key', 'P_PARTKEY');
        CALL set_table_property('PART', 'colocate_with', 'LINEITEM');
        CALL set_table_property('PART', 'bitmap_columns', 'P_PARTKEY,P_SIZE,P_NAME,P_MFGR,P_BRAND,P_TYPE,P_CONTAINER,P_COMMENT');
        CALL set_table_property('PART', 'dictionary_encoding_columns', 'P_NAME,P_MFGR,P_BRAND,P_TYPE,P_CONTAINER,P_COMMENT');
        CALL set_table_property('PART', 'time_to_live_in_seconds', '31536000');
        COMMIT;
        
        
        
        DROP TABLE IF EXISTS CUSTOMER;
        BEGIN;
        CREATE TABLE CUSTOMER
        (
            C_CUSTKEY    INT    NOT NULL PRIMARY KEY,
            C_NAME       TEXT   NOT NULL,
            C_ADDRESS    TEXT   NOT NULL,
            C_NATIONKEY  INT    NOT NULL,
            C_PHONE      TEXT   NOT NULL,
            C_ACCTBAL    DECIMAL(15,2) NOT NULL,
            C_MKTSEGMENT TEXT   NOT NULL,
            C_COMMENT    TEXT   NOT NULL
        );
        CALL set_table_property('CUSTOMER', 'distribution_key', 'C_CUSTKEY');
        CALL set_table_property('CUSTOMER', 'colocate_with', 'LINEITEM');
        CALL set_table_property('CUSTOMER', 'bitmap_columns', 'C_CUSTKEY,C_NATIONKEY,C_NAME,C_ADDRESS,C_PHONE,C_MKTSEGMENT,C_COMMENT');
        CALL set_table_property('CUSTOMER', 'dictionary_encoding_columns', 'C_NAME,C_ADDRESS,C_PHONE,C_MKTSEGMENT,C_COMMENT');
        CALL set_table_property('CUSTOMER', 'time_to_live_in_seconds', '31536000');
        COMMIT;
        
        DROP TABLE IF EXISTS SUPPLIER;
        
        BEGIN;
        CREATE TABLE SUPPLIER
        (
            S_SUPPKEY   INT    NOT NULL PRIMARY KEY,
            S_NAME      TEXT   NOT NULL,
            S_ADDRESS   TEXT   NOT NULL,
            S_NATIONKEY INT    NOT NULL,
            S_PHONE     TEXT   NOT NULL,
            S_ACCTBAL   DECIMAL(15,2) NOT NULL,
            S_COMMENT   TEXT   NOT NULL
        );
        CALL set_table_property('SUPPLIER', 'distribution_key', 'S_SUPPKEY');
        CALL set_table_property('SUPPLIER', 'colocate_with', 'LINEITEM');
        CALL set_table_property('SUPPLIER', 'bitmap_columns', 'S_SUPPKEY,S_NAME,S_ADDRESS,S_NATIONKEY,S_PHONE,S_COMMENT');
        CALL set_table_property('SUPPLIER', 'dictionary_encoding_columns', 'S_NAME,S_ADDRESS,S_PHONE,S_COMMENT');
        CALL set_table_property('SUPPLIER', 'time_to_live_in_seconds', '31536000');
        COMMIT;
        
        DROP TABLE IF EXISTS NATION;
        
        BEGIN;
        CREATE TABLE NATION(
          N_NATIONKEY INT NOT NULL PRIMARY KEY,
          N_NAME text NOT NULL,
          N_REGIONKEY INT NOT NULL,
          N_COMMENT text NOT NULL
        );
        CALL set_table_property('NATION', 'distribution_key', 'N_NATIONKEY');
        CALL set_table_property('NATION', 'colocate_with', 'LINEITEM');
        CALL set_table_property('NATION', 'bitmap_columns', 'N_NATIONKEY,N_NAME,N_REGIONKEY,N_COMMENT');
        CALL set_table_property('NATION', 'dictionary_encoding_columns', 'N_NAME,N_COMMENT');
        CALL set_table_property('NATION', 'time_to_live_in_seconds', '31536000');
        COMMIT;
        
        DROP TABLE IF EXISTS REGION;
        
        BEGIN;
        CREATE TABLE REGION
        (
            R_REGIONKEY INT  NOT NULL PRIMARY KEY,
            R_NAME      TEXT NOT NULL,
            R_COMMENT   TEXT
        );
        CALL set_table_property('REGION', 'distribution_key', 'R_REGIONKEY');
        CALL set_table_property('REGION', 'colocate_with', 'LINEITEM');
        CALL set_table_property('REGION', 'bitmap_columns', 'R_REGIONKEY,R_NAME,R_COMMENT');
        CALL set_table_property('REGION', 'dictionary_encoding_columns', 'R_NAME,R_COMMENT');
        CALL set_table_property('REGION', 'time_to_live_in_seconds', '31536000');
        COMMIT;
        ```

        You can run the `tpch_1sf=# \dt` command on the PostgreSQL client to check whether the tables are created.

        ```
        tpch_1sf=# \dt
                       List of relations
         Schema |   Name   | Type  |       Owner        
        --------+----------+-------+--------------------
         public | customer | table | tpch_1sf_developer
         public | lineitem | table | tpch_1sf_developer
         public | nation   | table | tpch_1sf_developer
         public | orders   | table | tpch_1sf_developer
         public | part     | table | tpch_1sf_developer
         public | partsupp | table | tpch_1sf_developer
         public | region   | table | tpch_1sf_developer
         public | supplier | table | tpch_1sf_developer
        (8 rows)
        ```

2.  **Import data**

    You can import data by using the following methods:

    -   In this topic, the `COPY FROM STDIN` statement is executed to import data. For more information about how to use the `COPY FROM STDIN` statement, see [Use the COPY statement to import on-premises data to Hologres](/intl.en-US/Data Access/Batch synchronization/A local file/Use the COPY statement to import on-premises data to Hologres.md). TPC-H data is imported from the **.tbl** files generated during [preparations](#table_8lb_57o_71n) to the tables created in Hologres.
    -   You can also run the following shell script in the directory of the data generation tool to import data. For more information about the parameters in the shell script, see [Connect to a Hologres instance from the PostgreSQL client](/intl.en-US/Common Development Tools/Connect to a Hologres instance from the PostgreSQL client.md).

        ```
        for i in `ls *.tbl`; do
            echo $i;
            name=`echo $i| cut -d'.' -f1`;
            PGUSER=<AccessID> PGPASSWORD=<AccessKey> psql -p <Port> -h <Endpoint> -d <Database> -c "COPY $name from stdin with delimiter '|' csv;" < $i;
        done
        ```

3.  **Collect characteristics of tables**

    To better execute queries, you can run the following commands on the PostgreSQL client to collect the characteristics of each table.

    ```
    vacuum  region;
    vacuum  nation;
    vacuum  supplier;
    vacuum  customer;
    vacuum  part;
    vacuum  partsupp;
    vacuum  orders;
    vacuum  lineitem;
    
    analyze nation;
    analyze region;
    analyze lineitem;
    analyze orders;
    analyze customer;
    analyze part;
    analyze partsupp;
    analyze supplier;
    ```

4.  **Execute queries**

    1.  Install pgbench, which facilitates statistics collection on queries. You can run the following command to install pgbench:

        ```
        yum install postgresql-contrib
        ```

        To prevent incompatibility, we recommend that you install pgbench V13 or later. If you have locally installed pgbench, make sure that its version is later than V9.6. To view the version of pgbench, run the following command:

        ```
        pgbench --version
        ```

    2.  To facilitate queries, you can download [the package of the 22 TPC-H query statements](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/207600/cn_zh/1621844924898/tpch_data_tpch_query.zip) and upload the package to the ECS instance. For more information about how to upload a file to ECS, see [Upload files to ECS instances](/intl.en-US/Deployment & Maintenance/Cloud assistant/Use the cloud assistant/Upload files to ECS instances.md).

    3.  Log on to the ECS instance, go to the directory of the uploaded package, and then run the following shell command to decompress the package:

        ```
        unzip tpch_data_tpch_query
        ```

    4.  You can execute TPC-H query statements by using the following methods. For more information about the query statements, see [22 TPC-H query statements](#section_ouf_xgk_s2q).

        -   Use pgbench to execute query statements one by one.

            The TPC-H benchmark test involves 22 query statements. For example, if you want to execute Query statement Q6, specify `6.sql` in the following command:

            ```
            PGUSER=<AccessID> PGPASSWORD=<AccessKey> pgbench -h <Endpoint> -p <Port> -d <Database> -c <Client_Num> -t <Query_Num> -n -f xxx.sql
            ```

        -   Run the following shell script to execute 22 query statements at a time and export the results to the hologres\_tpch\_test.out file:

            ```
            rm -f hologres_tpch_test.out
            echo `date +"%Y-%m-%d %H:%M:%S"` begin >> ./hologres_tpch_test.out
            for i in {1..22}
            do
                PGUSER=<AccessID> PGPASSWORD=<AccessKey> pgbench -h <Endpoint> -p <Port> -d <Database> -c <Client_Num> -t <Query_Num> -n -f ./tpch_data_tpch_query/${i}.sql >> ./hologres_tpch_test.out
            done
            ```

        The following table describes the parameters in the shell script.

        |Parameter|Description|
        |---------|-----------|
        |PGUSER|The AccessKey ID of your Alibaba Cloud account. You can obtain the AccessKey ID from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page. |
        |PGPASSWORD|The AccessKey secret of your Alibaba Cloud account. You can obtain the AccessKey secret from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page. |
        |-h|The domain name of the Hologres instance. This alias is defined for the Endpoint parameter. You can view the domain name of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
        |-p|The port number of the Hologres instance. This alias is defined for the Port parameter. You can view the port number of the Hologres instance on the Configurations tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
        |-d|The name of the database. This alias is defined for the Database parameter.|
        |-c|The number of clients to be tested. This alias is defined for the Client\_Num parameter. In this example, you need to test only query performance, but not the concurrency. Therefore, set this parameter to 1. |
        |-t|The number of queries to be executed on each client. This alias is defined for the Query\_Num parameter. In this example, set this parameter to 50.|
        |-f|The SQL statement to be executed.         -   You can execute a specified SQL statement defined in a file named in the format of `xxx.sql`. The TPC-H benchmark test involves 22 query statements. For example, Query statement Q6 is defined in the `6.sql` file.
        -   If you want to execute all query statements at a time, you do not need to change the value of this parameter in the script.
For more information about the 22 TPC-H query statements, see [22 TPC-H query statements](#section_ouf_xgk_s2q).|

    5.  You can check the query results in the hologres\_tpch\_test.out file. The `transaction type` parameter indicates the SQL file that was executed. The `latency average` parameter indicates the average response time of the three queries that were executed based on the SQL file.

        ```
        2021-03-23 03:50:54 begin
        pghost: hgpostcn-cn-oew21c935002-cn-hangzhou.hologres.aliyuncs.com pgport: 80 nclients: 1 nxacts: 3 dbName: tpch_100
        transaction type: ./tpch_data_tpch_query/1.sql
        scaling factor: 1
        query mode: simple
        number of clients: 1
        number of threads: 1
        number of transactions per client: 3
        number of transactions actually processed: 3/3
        latency average = 76.936 ms
        tps = 12.997850 (including connections establishing)
        tps = 15.972757 (excluding connections establishing)
        ...
        ```


## Performance test on point queries of key-value pairs

Before you test the performance of point queries of key-value pairs, you must create a table and import data to the table. After data is imported, you can generate and execute a query statement to run the test. To run a performance test on point queries of key-value pairs, perform the following steps:

1.  **Create a table**

    You can create a table based on the schema defined for the ORDERS table and use the database created for the performance test on OLAP queries to run the performance test on point queries of key-value pairs. After you connect to the Hologres instance from the PostgreSQL client, you can execute the following statements to create the table required for the test. For more information about how to connect to a Hologres instance from the PostgreSQL client, see [Connect to a Hologres instance from the PostgreSQL client](/intl.en-US/Common Development Tools/Connect to a Hologres instance from the PostgreSQL client.md).

    **Note:** The performance test on point queries of key-value pairs requires a row-oriented table. Therefore, the tables created for the performance test on OLAP queries cannot be used. You must create a row-oriented table.

    ```
    DROP TABLE IF EXISTS orders_row;
    
    BEGIN;
    CREATE TABLE public.orders_row (
     "o_orderkey" int8 NOT NULL,
     "o_custkey" int8,
     "o_orderstatus" bpchar(1),
     "o_totalprice" numeric(15,2),
     "o_orderdate" date,
     "o_orderpriority" bpchar(15),
     "o_clerk" bpchar(15),
     "o_shippriority" int8,
     "o_comment" varchar(79),
    PRIMARY KEY (o_orderkey)
    );
    CALL SET_TABLE_PROPERTY('public.orders_row', 'orientation', 'row');
    CALL SET_TABLE_PROPERTY('public.orders_row', 'clustering_key', 'o_orderkey');
    CALL SET_TABLE_PROPERTY('public.orders_row', 'time_to_live_in_seconds', '3153600000');
    CALL SET_TABLE_PROPERTY('public.orders_row', 'distribution_key', 'o_orderkey');
    COMMIT;
    ```

2.  **Import data**

    You can import data by using the following methods:

    -   Execute the COPY statement to import data
        -   In this topic, the `COPY FROM STDIN` statement is executed to import data. For more information about how to use the `COPY FROM STDIN` statement, see [Use the COPY statement to import on-premises data to Hologres](/intl.en-US/Data Access/Batch synchronization/A local file/Use the COPY statement to import on-premises data to Hologres.md). TPC-H data is imported from the **.tbl** files generated during [preparations](#table_8lb_57o_71n) to the table created in Hologres.
        -   You can also run the following shell script in the directory of the data generation tool to import data. For more information about the parameters in the shell script, see [Connect to a Hologres instance from the PostgreSQL client](/intl.en-US/Common Development Tools/Connect to a Hologres instance from the PostgreSQL client.md).

            ```
            PGUSER=<AccessID> PGPASSWORD=<AccessKey> psql -p <Port> -h <Endpoint> -d <Database> -c "COPY public.orders_row from stdin with delimiter '|' csv;" < orders.tbl>
            ```

    -   Execute the INSERT INTO statement to import data

        The data for the ORDERS table has been imported during the performance test on OLAP queries. You can run the following SQL statement to import the data to the row-oriented table:

        ```
        INSERT INTO public.orders_row
        SELECT  *
        FROM    public.orders;
        ```

3.  **Execute queries**

    1.  Generate a query statement

        The following table describes the two types of queries involved in the performance test on point queries of key-value pairs.

        |Query type|Query statement|Description|
        |----------|---------------|-----------|
        |Single-value filtering|        ```
SELECT  column_a
        ,column_b
        ,...
        ,column_x
FROM    table_x
WHERE   pk = value_x
;
        ```

|This query statement adopts single-value filtering where only one primary key value is specified in the `WHERE` clause.|
        |Multi-value filtering|        ```
SELECT  column_a
        ,column_b
        ,...
        ,column_x
FROM    table_x
WHERE   pk IN ( value_a, value_b,..., value_x )
;
        ```

|This query statement adopts multi-value filtering where multiple primary key values are specified in the `WHERE` clause.|

        You can run the following script to generate the required SQL statement. Two SQL statements are generated. The SQL statement that adopts single-value filtering is exported to the `kv_query_single.sql` file. The SQL statement that adopts multi-value filtering is exported to the `kv_query_in.sql` file, where 10 random primary key values are specified in the WHERE clause.

        ```
        rm -rf kv_query
        mkdir kv_query
        cd kv_query
        echo '\set column_values random(1,99999999)
        select O_ORDERKEY,O_CUSTKEY,O_ORDERSTATUS,O_TOTALPRICE,O_ORDERDATE,O_ORDERPRIORITY,O_CLERK,O_SHIPPRIORITY,O_COMMENT from public.orders_row WHERE o_orderkey =:column_values;' >> kv_query_single.sql
        echo '\set column_values1 random(1,99999999)
        \set column_values2 random(1,99999999)
        \set column_values3 random(1,99999999)
        \set column_values4 random(1,99999999)
        \set column_values5 random(1,99999999)
        \set column_values6 random(1,99999999)
        \set column_values7 random(1,99999999)
        \set column_values8 random(1,99999999)
        \set column_values9 random(1,99999999)
        \set column_values10 random(1,99999999)
        select O_ORDERKEY,O_CUSTKEY,O_ORDERSTATUS,O_TOTALPRICE,O_ORDERDATE,O_ORDERPRIORITY,O_CLERK,O_SHIPPRIORITY,O_COMMENT from public.orders_row WHERE o_orderkey in(:column_values1,:column_values2,:column_values3,:column_values4,:column_values5,:column_values6,:column_values7,:column_values8,:column_values9,:column_values10);' >> kv_query_in.sql
        ```

    2.  Install pgbench, which facilitates statistics collection on queries. You can run the following command to install pgbench:

        ```
        yum install postgresql-contrib
        ```

        To prevent incompatibility, we recommend that you install pgbench V13 or later. If you have locally installed pgbench, make sure that its version is later than V9.6. To view the version of pgbench, run the following command:

        ```
        pgbench --version
        ```

    3.  Execute the query statement

        -   Use pgbench to execute the query statement that adopts single-value filtering. You must run the following command in the directory where the SQL file resides. For more information about the parameters in the command, see the [table](#table_uht_ipk_pw3) in the "Performance test on OLAP queries" section.

            ```
            PGUSER=<AccessID> PGPASSWORD=<AccessKey> pgbench -h <Endpoint> -p <Port> -d <Database> -c <Client_Num> -t <Query_Num> -n -f kv_query_single.sql
            ```

        -   Use pgbench to execute the query statement that adopts multi-value filtering. You must run the following command in the directory where the SQL file resides. For more information about the parameters in the command, see the [table](#table_uht_ipk_pw3) in the "Performance test on OLAP queries" section.

            ```
            PGUSER=<AccessID> PGPASSWORD=<AccessKey> pgbench -h <Endpoint> -p <Port> -d <Database> -c <Client_Num> -t <Query_Num> -n -f kv_query_in.sql
            ```


## 22 TPC-H query statements

The following table provides the links to the 22 TPC-H query statements. To view a specific query statement, you can click the corresponding link in the table.

|Item|Query statement|
|----|---------------|
|22 TPC-H query statements|[Q1](#li_fiz_p0h_7uq)|[Q2](#li_g51_ap1_xcx)|[Q3](#li_ek8_r6b_81l)|[Q4](#li_kd3_gst_x3n)|
|[Q5](#li_hop_9g3_wz0)|[Q6](#li_axq_yv9_19q)|[Q7](#li_4re_ivq_wr5)|[Q8](#li_3qg_zsc_aw4)|
|[Q9](#li_bpk_imt_tjn)|[Q10](#li_ole_jxw_aon)|[Q11](#li_vr0_rds_r6r)|[Q12](#li_crw_xh1_k5k)|
|[Q13](#li_24w_qmi_9rb)|[Q14](#li_a23_0w0_8x4)|[Q15](#li_pc1_6fb_i7x)|[Q16](#li_zbb_vhz_6kg)|
|[Q17](#li_uet_lnu_jkh)|[Q18](#li_2eq_bne_7iq)|[Q19](#li_re4_i5f_f4v)|[Q20](#li_3lm_cj7_tp5)|
|[Q21](#li_a2u_orx_8yd)|[Q22](#li_6yn_xwb_l7e)|N/A|N/A|

-   Q1

    ```
    select
        l_returnflag,
        l_linestatus,
        sum(l_quantity) as sum_qty,
        sum(l_extendedprice) as sum_base_price,
        sum(l_extendedprice * (1 - l_discount)) as sum_disc_price,
        sum(l_extendedprice * (1 - l_discount) * (1 + l_tax)) as sum_charge,
        avg(l_quantity) as avg_qty,
        avg(l_extendedprice) as avg_price,
        avg(l_discount) as avg_disc,
        count(*) as count_order
    from
        lineitem
    where
        l_shipdate <= date '1998-12-01' - interval '90' day 
    group by
        l_returnflag,
        l_linestatus
    order by
        l_returnflag,
        l_linestatus;
    ```

-   Q2

    ```
    select
        s_acctbal,
        s_name,
        n_name,
        p_partkey,
        p_mfgr,
        s_address,
        s_phone,
        s_comment
    from
        part,
        supplier,
        partsupp,
        nation,
        region
    where
        p_partkey = ps_partkey
        and s_suppkey = ps_suppkey
        and p_size = 15
        and p_type like '%BRASS'
        and s_nationkey = n_nationkey
        and n_regionkey = r_regionkey
        and r_name = 'EUROPE'
        and ps_supplycost = (
            select
                min(ps_supplycost)
            from
                partsupp,
                supplier,
                nation,
                region
            where
                p_partkey = ps_partkey
                and s_suppkey = ps_suppkey
                and s_nationkey = n_nationkey
                and n_regionkey = r_regionkey
                and r_name = 'EUROPE'
        )
    order by
        s_acctbal desc,
        n_name,
        s_name,
        p_partkey
    limit 100;
    ```

-   Q3

    ```
    select
        l_orderkey,
        sum(l_extendedprice * (1 - l_discount)) as revenue,
        o_orderdate,
        o_shippriority
    from
        customer,
        orders,
        lineitem
    where
        c_mktsegment = 'BUILDING'
        and c_custkey = o_custkey
        and l_orderkey = o_orderkey
        and o_orderdate < date '1995-03-15'
        and l_shipdate > date '1995-03-15'
    group by
        l_orderkey,
        o_orderdate,
        o_shippriority
    order by
        revenue desc,
        o_orderdate
    limit 10;
    ```

-   Q4

    ```
    select
        o_orderpriority,
        count(*) as order_count
    from
        orders
    where
        o_orderdate >= date '1993-07-01'
        and o_orderdate < date '1993-07-01' + interval '3' month
        and exists (
            select
                *
            from
                lineitem
            where
                l_orderkey = o_orderkey
                and l_commitdate < l_receiptdate
        )
    group by
        o_orderpriority
    order by
        o_orderpriority;
    ```

-   Q5

    ```
    select
        n_name,
        sum(l_extendedprice * (1 - l_discount)) as revenue
    from
        customer,
        orders,
        lineitem,
        supplier,
        nation,
        region
    where
        c_custkey = o_custkey
        and l_orderkey = o_orderkey
        and l_suppkey = s_suppkey
        and c_nationkey = s_nationkey
        and s_nationkey = n_nationkey
        and n_regionkey = r_regionkey
        and r_name = 'ASIA'
        and o_orderdate >= date '1994-01-01'
        and o_orderdate < date '1994-01-01' + interval '1' year
    group by
        n_name
    order by
        revenue desc;
    ```

-   Q6

    ```
    select
        sum(l_extendedprice * l_discount) as revenue
    from
        lineitem
    where
        l_shipdate >= date '1994-01-01'
        and l_shipdate < date '1994-01-01' + interval '1' year
        and l_discount between 6 - 1 and 6 + 1
        and l_quantity < 2400
    ```

-   Q7

    ```
    set hg_experimental_enable_double_equivalent=on;
    select
        supp_nation,
        cust_nation,
        l_year,
        sum(volume) as revenue
    from
        (
            select
                n1.n_name as supp_nation,
                n2.n_name as cust_nation,
                extract(year from l_shipdate) as l_year,
                l_extendedprice * (1 - l_discount) as volume
            from
                supplier,
                lineitem,
                orders,
                customer,
                nation n1,
                nation n2
            where
                s_suppkey = l_suppkey
                and o_orderkey = l_orderkey
                and c_custkey = o_custkey
                and s_nationkey = n1.n_nationkey
                and c_nationkey = n2.n_nationkey
                and (
                    (n1.n_name = 'FRANCE' and n2.n_name = 'GERMANY')
                    or (n1.n_name = 'GERMANY' and n2.n_name = 'FRANCE')
                )
                and l_shipdate between date '1995-01-01' and date '1996-12-31'
        ) as shipping
    group by
        supp_nation,
        cust_nation,
        l_year
    order by
        supp_nation,
        cust_nation,
        l_year;
    ```

-   Q8

    ```
    set hg_experimental_enable_double_equivalent=on;
    select
        o_year,
        sum(case
            when nation = 'BRAZIL' then volume
            else 0
        end) / sum(volume) as mkt_share
    from
        (
            select
                extract(year from o_orderdate) as o_year,
                l_extendedprice * (1 - l_discount) as volume,
                n2.n_name as nation
            from
                part,
                supplier,
                lineitem,
                orders,
                customer,
                nation n1,
                nation n2,
                region
            where
                p_partkey = l_partkey
                and s_suppkey = l_suppkey
                and l_orderkey = o_orderkey
                and o_custkey = c_custkey
                and c_nationkey = n1.n_nationkey
                and n1.n_regionkey = r_regionkey
                and r_name = 'AMERICA'
                and s_nationkey = n2.n_nationkey
                and o_orderdate between date '1995-01-01' and date '1996-12-31'
                and p_type = 'STANDARD POLISHED TIN'
        ) as all_nations
    group by
        o_year
    order by
        o_year;
    ```

-   Q9

    ```
    set hg_experimental_enable_double_equivalent=on;
    select
        nation,
        o_year,
        sum(amount) as sum_profit
    from
        (
            select
                n_name as nation,
                extract(year from o_orderdate) as o_year,
                l_extendedprice * (1 - l_discount) - ps_supplycost * l_quantity as amount
            from
                part,
                supplier,
                lineitem,
                partsupp,
                orders,
                nation
            where
                s_suppkey = l_suppkey
                and ps_suppkey = l_suppkey
                and ps_partkey = l_partkey
                and p_partkey = l_partkey
                and o_orderkey = l_orderkey
                and s_nationkey = n_nationkey
                and p_name like '%green%'
        ) as profit
    group by
        nation,
        o_year
    order by
        nation,
        o_year desc;
    ```

-   Q10

    ```
    select
        c_custkey,
        c_name,
        sum(l_extendedprice * (1 - l_discount)) as revenue,
        c_acctbal,
        n_name,
        c_address,
        c_phone,
        c_comment
    from
        customer,
        orders,
        lineitem,
        nation
    where
        c_custkey = o_custkey
        and l_orderkey = o_orderkey
        and o_orderdate >= date '1993-10-01'
        and o_orderdate < date '1993-10-01' + interval '3' month
        and l_returnflag = 'R'
        and c_nationkey = n_nationkey
    group by
        c_custkey,
        c_name,
        c_acctbal,
        c_phone,
        n_name,
        c_address,
        c_comment
    order by
        revenue desc
    limit 20;
    ```

-   Q11

    ```
    select
        ps_partkey,
        sum(ps_supplycost * ps_availqty) as value
    from
        partsupp,
        supplier,
        nation
    where
        ps_suppkey = s_suppkey
        and s_nationkey = n_nationkey
        and n_name = 'GERMANY'
    group by
        ps_partkey having
            sum(ps_supplycost * ps_availqty) > (
                select
                    sum(ps_supplycost * ps_availqty) * 0.0000010000
                from
                    partsupp,
                    supplier,
                    nation
                where
                    ps_suppkey = s_suppkey
                    and s_nationkey = n_nationkey
                    and n_name = 'GERMANY'
            )
    order by
        value desc
        limit 100;
    ```

-   Q12

    ```
    select
        l_shipmode,
        sum(case
            when o_orderpriority = '1-URGENT'
                or o_orderpriority = '2-HIGH'
                then 1
            else 0
        end) as high_line_count,
        sum(case
            when o_orderpriority <> '1-URGENT'
                and o_orderpriority <> '2-HIGH'
                then 1
            else 0
        end) as low_line_count
    from
        orders,
        lineitem
    where
        o_orderkey = l_orderkey
        and l_shipmode in ('MAIL', 'SHIP')
        and l_commitdate < l_receiptdate
        and l_shipdate < l_commitdate
        and l_receiptdate >= date '1994-01-01'
        and l_receiptdate < date '1994-01-01' + interval '1' year
    group by
        l_shipmode
    order by
        l_shipmode;
    ```

-   Q13

    ```
    select
        c_count,
        count(*) as custdist
    from
        (
            select
                c_custkey,
                count(o_orderkey)
            from
                customer left outer join orders on
                    c_custkey = o_custkey
                    and o_comment not like '%special%requests%'
            group by
                c_custkey
        ) as c_orders (c_custkey, c_count)
    group by
        c_count
    order by
        custdist desc,
        c_count desc;
    ```

-   Q14

    ```
    select
        100.00 * sum(case
            when p_type like 'PROMO%'
                then l_extendedprice * (1 - l_discount)
            else 0
        end) / sum(l_extendedprice * (1 - l_discount)) as promo_revenue
    from
        lineitem,
        part
    where
        l_partkey = p_partkey
        and l_shipdate >= date '1995-09-01'
        and l_shipdate < date '1995-09-01' + interval '1' month;
    ```

-   Q15

    ```
    with revenue0(SUPPLIER_NO, TOTAL_REVENUE)  as
        (
        select
            l_suppkey,
            sum(l_extendedprice * (1 - l_discount))
        from
            lineitem
        where
            l_shipdate >= date '1995-12-01'
            and l_shipdate < date '1995-12-01' + interval '3' month
        group by
            l_suppkey
        )
    select
        s_suppkey,
        s_name,
        s_address,
        s_phone,
        total_revenue
    from
        supplier,
        revenue0
    where
        s_suppkey = supplier_no
        and total_revenue = (
            select
                max(total_revenue)
            from
                revenue0
        )
    order by
        s_suppkey;
    ```

-   Q16

    ```
    select
        p_brand,
        p_type,
        p_size,
        count(distinct ps_suppkey) as supplier_cnt
    from
        partsupp,
        part
    where
        p_partkey = ps_partkey
        and p_brand <> 'Brand#45'
        and p_type not like 'MEDIUM POLISHED%'
        and p_size in (49, 14, 23, 45, 19, 3, 36, 9)
        and ps_suppkey not in (
            select
                s_suppkey
            from
                supplier
            where
                s_comment like '%Customer%Complaints%'
        )
    group by
        p_brand,
        p_type,
        p_size
    order by
        supplier_cnt desc,
        p_brand,
        p_type,
        p_size;
    ```

-   Q17

    ```
    select
        sum(l_extendedprice) / 7.0 as avg_yearly
    from
        lineitem,
        part
    where
        p_partkey = l_partkey
        and p_brand = 'Brand#23'
        and p_container = 'MED BOX'
        and l_quantity < (
            select
                0.2 * avg(l_quantity)
            from
                lineitem
            where
                l_partkey = p_partkey
                and l_partkey in(
            select p_partkey 
            from part 
            where p_brand = 'Brand#23' and p_container = 'MED BOX')
        );
    ```

-   Q18

    ```
    select
        c_name,
        c_custkey,
        o_orderkey,
        o_orderdate,
        o_totalprice,
        sum(l_quantity)
    from
        customer,
        orders,
        lineitem
    where
        o_orderkey in (
            select
                l_orderkey
            from
                lineitem
            group by
                l_orderkey having
                    sum(l_quantity) > 300
        )
        and c_custkey = o_custkey
        and o_orderkey = l_orderkey
    group by
        c_name,
        c_custkey,
        o_orderkey,
        o_orderdate,
        o_totalprice
    order by
        o_totalprice desc,
        o_orderdate
    limit 100;
    ```

-   Q19

    ```
    select
        sum(l_extendedprice* (1 - l_discount)) as revenue
    from
        lineitem,
        part
    where
        (
            p_partkey = l_partkey
            and p_brand = 'Brand#12'
            and p_container in ('SM CASE', 'SM BOX', 'SM PACK', 'SM PKG')
            and l_quantity >= 1 and l_quantity <= 1 + 10
            and p_size between 1 and 5
            and l_shipmode in ('AIR', 'AIR REG')
            and l_shipinstruct = 'DELIVER IN PERSON'
        )
        or
        (
            p_partkey = l_partkey
            and p_brand = 'Brand#23'
            and p_container in ('MED BAG', 'MED BOX', 'MED PKG', 'MED PACK')
            and l_quantity >= 10 and l_quantity <= 10 + 10
            and p_size between 1 and 10
            and l_shipmode in ('AIR', 'AIR REG')
            and l_shipinstruct = 'DELIVER IN PERSON'
        )
        or
        (
            p_partkey = l_partkey
            and p_brand = 'Brand#34'
            and p_container in ('LG CASE', 'LG BOX', 'LG PACK', 'LG PKG')
            and l_quantity >= 20 and l_quantity <= 20 + 10
            and p_size between 1 and 15
            and l_shipmode in ('AIR', 'AIR REG')
            and l_shipinstruct = 'DELIVER IN PERSON'
        );
    ```

-   Q20

    ```
    select
        s_name,
        s_address
    from
        supplier,
        nation
    where
        s_suppkey in (
            select
                ps_suppkey
            from
                partsupp
            where
                ps_partkey in (
                    select
                        p_partkey
                    from
                        part
                    where
                        p_name like 'forest%'
                )
                and ps_availqty > (
                    select
                        0.5 * sum(l_quantity)
                    from
                        lineitem
                    where
                        l_partkey = ps_partkey
                        and l_suppkey = ps_suppkey
                        and l_shipdate >= date '1994-01-01'
                        and l_shipdate < date '1994-01-01' + interval '1' year
                )
        )
        and s_nationkey = n_nationkey
        and n_name = 'CANADA'
    order by
        s_name;
    ```

-   Q21

    ```
    select
        s_name,
        count(*) as numwait
    from
        supplier,
        lineitem l1,
        orders,
        nation
    where
        s_suppkey = l1.l_suppkey
        and o_orderkey = l1.l_orderkey
        and o_orderstatus = 'F'
        and l1.l_receiptdate > l1.l_commitdate
        and exists (
            select
                *
            from
                lineitem l2
            where
                l2.l_orderkey = l1.l_orderkey
                and l2.l_suppkey <> l1.l_suppkey
        )
        and not exists (
            select
                *
            from
                lineitem l3
            where
                l3.l_orderkey = l1.l_orderkey
                and l3.l_suppkey <> l1.l_suppkey
                and l3.l_receiptdate > l3.l_commitdate
        )
        and s_nationkey = n_nationkey
        and n_name = 'SAUDI ARABIA'
    group by
        s_name
    order by
        numwait desc,
        s_name
    limit 100;
    ```

-   Q22

    ```
    select
        cntrycode,
        count(*) as numcust,
        sum(c_acctbal) as totacctbal
    from
        (
            select
                substring(c_phone from 1 for 2) as cntrycode,
                c_acctbal
            from
                customer
            where
                substring(c_phone from 1 for 2) in
                    ('13', '31', '23', '29', '30', '18', '17')
                and c_acctbal > (
                    select
                        avg(c_acctbal)
                    from
                        customer
                    where
                        c_acctbal > 0.00
                        and substring(c_phone from 1 for 2) in
                            ('13', '31', '23', '29', '30', '18', '17')
                )
                and not exists (
                    select
                        *
                    from
                        orders
                    where
                        o_custkey = c_custkey
                )
        ) as custsale
    group by
        cntrycode
    order by
        cntrycode;
    ```


