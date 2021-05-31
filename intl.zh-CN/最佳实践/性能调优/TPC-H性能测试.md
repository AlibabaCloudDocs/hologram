---
keyword: [TPC-H, OLAP查询场景, Key/Value点查场景]
---

# TPC-H性能测试

本文将为您介绍如何使用TPC-H（商业智能计算测试）对OLAP查询场景和Key/Value点查场景进行性能测试。

## TPC-H简介

TPC-H是美国交易处理效能委员会TCP（Transaction Processing Performance Council）组织制定的用来模拟决策支持类应用的一个测试集。目前在学术界和工业界普遍采用它来评价决策支持技术方面应用的性能。TPC-H根据真实的生产运行环境来建模，模拟了一套销售系统的数据仓库。该测试共包含8张表，数据量可设定从1 GB~3 TB不等。其基准测试共包含了22个查询，主要评价指标为各个查询的响应时间，即从提交查询到结果返回所需时间，测试结果可综合反映系统处理查询时的能力。

TPC-H的测试数据集共包含8张表，相互关系如下图所示。

![TPCH数据表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7987381261/p276682.png)

## 场景说明

本测试场景主要包含如下内容：

-   [OLAP查询场景测试](#section_faj_zuf_r1h)，主要使用列存表，直接使用TPC-H测试中的22条查询语句进行测试。
-   [Key/Value点查场景测试](#section_g4g_vro_v6x)，主要使用行存表，针对ORDERS使用行存表后，进行主键过滤的点查。

测试数据量会直接影响测试结果，TPC-H的生成工具中使用SF（scale factor）控制生成数据量的大小，1SF对应1GB。

**说明：** 以上的数据量仅针对**原始数据的数据量**，不包括索引等空间占用，因此在准备环境时，您需要预留更多的空间。

## 准备工作

您需要准备OLAP查询场景和Key/Value点查场景所需的基础环境和TCP-H数据。具体内容如下：

-   **基础环境准备**

    |操作步骤|操作说明|
    |----|----|
    |1.创建ECS实例|登录阿里云，创建一个ECS实例，用于数据生成、向Hologres导入数据和客户端测试。建议规格具体如下：    -   ecs.g6.4xlarge规格。
    -   CentOS 7.9系统。
    -   ESSD数据盘，具体数据容量根据需要测试的数据量大小决定。
    -   建议ECS与Hologres实例在相同地域，使用相同的VPC网络。
更多关于创建ECS的操作，请参见[创建实例](/intl.zh-CN/实例/创建实例/创建方式导航.md)。|
    |2.创建Hologres实例|本次测试环境使用了独享（按量付费）的实例，由于该实例仅用于测试使用，计算资源配置选择8核32G。您可以根据实际业务需求，选择计算资源配置。

登录阿里云，进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/overview)，单击**新增引擎实例**。

更多关于创建Hologres实例的操作，请参见[购买Hologres](/intl.zh-CN/准备工作/购买Hologres.md)。 |
    |3.创建测试数据库|您需要登录创建的Hologres实例，创建一个数据库。本文示例中命名数据库为**tpch\_1sf**。更多关于创建数据库的操作，请参见[新建数据库](/intl.zh-CN/连接开发工具/HoloWeb/连接管理/数据库.md)。 |

-   **生成TCP-H数据**

    您需要准备数据生成工具，即通过远程连接ECS实例，实现下载、编译并生成TCP-H数据。具体说明如下：

    |操作步骤|操作说明|
    |----|----|
    |1.准备数据生成工具|准备数据生成工具，您需要执行如下内容：    1.  远程连接ECS实例。

更多关于连接方式的说明，请参见[连接方式概述](/intl.zh-CN/实例/连接实例/连接方式概述.md)。

    2.  连接实例后，执行`yum update`，更新所有库。
    3.  执行`yum install git`，安装git。
    4.  执行`yum install gcc`，安装gcc。
    5.  执行`git clone https://github.com/gregrahn/tpch-kit.git`，下载TPC-H数据生成代码。
    6.  执行`cd tpch-kit/dbgen`，进入数据生成工具代码目录。
    7.  执行`make`，编译数据生成工具代码。 |
    |2.生成数据|编译成功后，您可以执行`./dbgen --help`查看代码生成工具的相关参数。您可以执行如下内容生成TPC-H数据用于下文的测试，同时您也可以查看生成的文件：    -   本文示例仅生成1 GB数据，您可以运行如下代码生成数据。

```
./dbgen -vf -s 1
```

如果您需要生成更多数据量的数据，可以调整SF的参数。例如，您可以使用如下代码生成1 TB数据。

```
./dbgen -vf -s 1000
```

一般情况下，32CU可以运行TPC-H SF10的数据量，256CU可以运行TPC-H SF50的数据量。

    -   生成的数据存储在ECS中，您可以在数据生成后使用如下代码查看生成的文件。可以看到生成工具生成了8个数据文件，每个数据文件都对应一张数据集中的表。

        ```
ls | grep '.*.tbl'
        ``` |


## OLAP查询场景测试

OLAP查询场景测试，您需要创建表并导入数据。数据导入完成后，为更好的执行查询，您可以收集各张表的特征信息，然后使用pgbench工具进行查询测试。具体步骤如下：

1.  **创建表**

    1.  本文内容主要基于psql进行数据导入操作，您需要先在ECS中运行如下命令安装psql。

        ```
        yum install postgresql-server
        ```

    2.  安装psql后，您可以使用如下命令登录Hologres实例。更多关于参数的说明，请参见[连接psql参数说明](/intl.zh-CN/连接开发工具/psql客户端.md)。

        ```
        PGUSER=<AccessID> PGPASSWORD=<AccessKey> psql -p <Port> -h <Endpoint> -d <Database>
        ```

    3.  使用psql连接Hologres后，您可以使用如下建表语句创建数据库表。

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

        使用psql连接Hologres后，您可以使用如下建表语句创建数据库表。数据表创建完毕后，您可以在psql中执行`tpch_1sf=# \dt`查看是否创建成功。

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

2.  **导入数据**

    您可以使用如下方式导入数据：

    -   本文主要使用`COPY FROM STDIN`的方式导入数据。更多关于`COPY FROM STDIN`的详细操作，请参见[使用COPY命令导入本地数据至Hologres](/intl.zh-CN/数据接入/离线同步/本地文件/使用COPY命令导入本地数据至Hologres.md)。此处会将**tbl**数据文件导入Hologres创建的表中，**tbl**为[准备工作](#table_8lb_57o_71n)中生成的TPC-H数据。
    -   您也可以在数据生成工具的目录中，使用如下shell脚本导入数据。更多关于参数的说明，请参见[参数说明](/intl.zh-CN/连接开发工具/psql客户端.md)。

        ```
        for i in `ls *.tbl`; do
            echo $i;
            name=`echo $i| cut -d'.' -f1`;
            PGUSER=<AccessID> PGPASSWORD=<AccessKey> psql -p <Port> -h <Endpoint> -d <Database> -c "COPY $name from stdin with delimiter '|' csv;" < $i;
        done
        ```

3.  **收集统计信息**

    为了更好的执行查询，您可以在psql中使用如下语句，收集各张表的特征信息。

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

4.  **执行查询**

    1.  为了方便统计查询信息，您需要使用pgbench工具。您可以使用如下命令安装pgbench工具。

        ```
        yum install postgresql-contrib
        ```

        为了避免因工具不兼容影响测试，请您安装版本为13及以上的pgbench工具。如果您本地已经安装pgbench工具，请确保其版本为9.6以上。您可以通过执行如下命令查看当前工具版本。

        ```
        pgbench --version
        ```

    2.  为了方便查询，您可以直接单击[下载TCPH 22条查询语句](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/207600/cn_zh/1621844924898/tpch_data_tpch_query.zip)，并将下载的语句上传至ECS。更多关于上传数据的操作，请参见[上传本地文件到ECS实例](/intl.zh-CN/运维与监控/云助手/使用云助手/上传本地文件到ECS实例.md)。

    3.  进入ECS，并访问上传文件的目录，使用如下shell命令解压缩文件。

        ```
        unzip tpch_data_tpch_query
        ```

    4.  您可以使用如下方式执行TPC-H的查询语句。查询语句具体内容，请参见[TPC-H 22条查询语句](#section_ouf_xgk_s2q)。

        -   使用pgbench工具，执行单条查询。

            TCP-H共有22条语句，例如您需要执行Q6语句，则具体语句为`6.sql`

            ```
            PGUSER=<AccessID> PGPASSWORD=<AccessKey> pgbench -h <Endpoint> -p <Port> -d <Database> -c <Client_Num> -t <Query_Num> -n -f xxx.sql
            ```

        -   使用shell脚本，批量执行22条查询，并将结果输出到文件hologres\_tpch\_test.out中。

            ```
            rm -f hologres_tpch_test.out
            echo `date +"%Y-%m-%d %H:%M:%S"` begin >> ./hologres_tpch_test.out
            for i in {1..22}
            do
                PGUSER=<AccessID> PGPASSWORD=<AccessKey> pgbench -h <Endpoint> -p <Port> -d <Database> -c <Client_Num> -t <Query_Num> -n -f ./tpch_data_tpch_query/${i}.sql >> ./hologres_tpch_test.out
            done
            ```

        具体参数说明如下所示：

        |参数|说明|
        |--|--|
        |PGUSER|当前阿里云账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。 |
        |PGPASSWORD|当前阿里云账号的AccessKey Secret。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。 |
        |-h|Hologres实例的网络地址（Endpoint）。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取网络地址。 |
        |-p|Hologres实例的端口（Port）。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取实例端 |
        |-d|Hologres指定实例中的数据库名（Database）。|
        |-c|客户端数目，即并发度（Client\_Num）。本示例取值为1，由于该测试仅测试查询性能，不测试并发，将并发度置为1即可。 |
        |-t|每个客户端需要执行的压测Query数量（Query\_Num）。例如本示例取值为50。|
        |-f|压力测试的SQL语句。        -   当您执行单条查询时，执行语句为`xxx.sql`TCP-H共有22条语句，如您需要执行Q6语句，则具体语句为`6.sql`
        -   当您执行批量查询时，批量脚本中该参数的取值无需修改。
TPC-H 22条查询语句具体内容，请参见[TPC-H 22条查询语句](#section_ouf_xgk_s2q)。|

    5.  您可以通过查看hologres\_tpch\_test.out得到查询结果。其中，`transaction type`表示执行具体的SQL文件。`latency average`表示对应SQL文件的3次查询的平均时间。

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


## Key/Value点查场景测试

Key/Value点查场景测试，您需要创建表并导入数据。数据导入完成后，可以生成查询语句并进行查询测试。具体步骤如下：

1.  **创建表**

    Key/Value点查场景测试继续使用OLAP查询场景创建的数据库，使用TPCH数据集中的ORDERS表进行测试。您在使用psql连接Hologres后即可运行如下命令创建表。psql连接Hologres操作，请参见[连接psql参数说明](/intl.zh-CN/连接开发工具/psql客户端.md)。

    **说明：** 由于点查场景需要使用行存表，所以不能使用OLAP查询场景中使用的表，需要新创建一张表。

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

2.  **导入数据**

    您可以使用如下方式导入数据：

    -   COPY方式导入数据
        -   本文主要使用`COPY FROM STDIN`的方式导入数据。更多关于`COPY FROM STDIN`的详细操作，请参见[使用COPY命令导入本地数据至Hologres](/intl.zh-CN/数据接入/离线同步/本地文件/使用COPY命令导入本地数据至Hologres.md)。此处会将**tbl**数据文件导入Hologres创建的表中，**tbl**为[准备工作](#d6e123)中生成的TPC-H数据。
        -   您也可以在数据生成工具的目录中，使用如下shell脚本导入数据。更多关于参数的说明，请参见[参数说明](/intl.zh-CN/连接开发工具/psql客户端.md)。

            ```
            PGUSER=<AccessID> PGPASSWORD=<AccessKey> psql -p <Port> -h <Endpoint> -d <Database> -c "COPY public.orders_row from stdin with delimiter '|' csv;" < orders.tbl>
            ```

    -   INSERT INTO方式导入数据

        在OLAP场景测试时已经导入了ORDERS表的数据，您可以运行如下SQL语句导入数据。

        ```
        INSERT INTO public.orders_row
        SELECT  *
        FROM    public.orders;
        ```

3.  **执行查询**

    1.  生成查询语句

        Key/Value点查场景主要有两种查询场景，具体查询语句如下：

        |查询方式|查询语句|说明|
        |----|----|--|
        |单值筛选|        ```
SELECT  column_a
        ,column_b
        ,...
        ,column_x
FROM    table_x
WHERE   pk = value_x
;
        ```

|此查询语句主要用于单值筛选，即`WHERE`的SQL语句取值唯一。|
        |多值筛选|        ```
SELECT  column_a
        ,column_b
        ,...
        ,column_x
FROM    table_x
WHERE   pk IN ( value_a, value_b,..., value_x )
;
        ```

|此查询语句主要用于多值筛选，即`WHERE`的SQL语句可以取多个值。|

        您可以使用如下脚本生成所需的SQL语句，该脚本会生成2条SQL语句。其中，`kv_query_single.sql`，表示单值筛选的SQL。`kv_query_in.sql`，表示多值筛选的SQL，该脚本会随机生成一个针对10个值筛选的SQL。

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

    2.  为了方便统计查询信息，您需要使用pgbench工具。您可以使用如下命令安装pgbench工具。

        ```
        yum install postgresql-contrib
        ```

        为了避免因工具不兼容影响测试，请您安装版本为13及以上的pgbench工具。如果您本地已经安装pgbench工具，请确保其版本为9.6以上。您可以通过执行如下命令查看当前工具版本。

        ```
        pgbench --version
        ```

    3.  执行测试语句

        -   针对单值筛选的场景，使用pgbench工具进行压测。您需要在生成SQL的目录执行如下命令，更多参数说明，请参见[参数说明](#table_uht_ipk_pw3)。

            ```
            PGUSER=<AccessID> PGPASSWORD=<AccessKey> pgbench -h <Endpoint> -p <Port> -d <Database> -c <Client_Num> -t <Query_Num> -n -f kv_query_single.sql
            ```

        -   针对多值筛选的场景，使用pgbench工具进行压测。您需要在生成SQL的目录执行如下命令，更多参数说明，请参见[参数说明](#table_uht_ipk_pw3)。

            ```
            PGUSER=<AccessID> PGPASSWORD=<AccessKey> pgbench -h <Endpoint> -p <Port> -d <Database> -c <Client_Num> -t <Query_Num> -n -f kv_query_in.sql
            ```

            查看


## TPC-H 22条查询语句

TPCH 22条查询语句如下所示，您可以单击表格中的链接进行查看。

|名称|查询语句|
|--|----|
|TPCH 22条查询语句|[Q1](#li_fiz_p0h_7uq)|[Q2](#li_g51_ap1_xcx)|[Q3](#li_ek8_r6b_81l)|[Q4](#li_kd3_gst_x3n)|
|[Q5](#li_hop_9g3_wz0)|[Q6](#li_axq_yv9_19q)|[Q7](#li_4re_ivq_wr5)|[Q8](#li_3qg_zsc_aw4)|
|[Q9](#li_bpk_imt_tjn)|[Q10](#li_ole_jxw_aon)|[Q11](#li_vr0_rds_r6r)|[Q12](#li_crw_xh1_k5k)|
|[Q13](#li_24w_qmi_9rb)|[Q14](#li_a23_0w0_8x4)|[Q15](#li_pc1_6fb_i7x)|[Q16](#li_zbb_vhz_6kg)|
|[Q17](#li_uet_lnu_jkh)|[Q18](#li_2eq_bne_7iq)|[Q19](#li_re4_i5f_f4v)|[Q20](#li_3lm_cj7_tp5)|
|[Q21](#li_a2u_orx_8yd)|[Q22](#li_6yn_xwb_l7e)|-|-|

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


