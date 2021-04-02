---
keyword: [SQL, Hologres, 分区表, 非分区表]
---

# 使用SQL导入MaxCompute的数据至Hologres

本文为您介绍如何使用SQL语句导入MaxCompute的数据至Hologres。

针对MaxCompute的业务数据大于200 GB，查询复杂度高且要求查询响应达到秒级的场景，Hologres支持直接导入数据并查询。

## 注意事项

通过SQL导入MaxCompute数据至Hologres时，您需要注意如下内容：

-   MaxCompute的分区与Hologres无强映射关系，Maxcompute的分区字段映射至Hologres为普通字段，因此支持MaxCompute分区数据导入Hologres非分区或者分区。
-   Hologres仅支持一级分区，MaxCompute多级分区导入Hologres分区表时，只需要映射一个分区，其余分区映射成Hologres的普通字段。
-   如果导入数据时需要更新覆盖原有数据，您需要使用[INSERT ON CONFLICT](/intl.zh-CN/SQL参考/DML&DQL/INSERT ON CONFLICT.md)语法。
-   MaxCompute与Hologres的数据类型映射，请参见[数据类型汇总](/intl.zh-CN/SQL参考/数据类型/数据类型汇总.md)

## 导入MaxCompute的非分区表数据至Hologres并查询

1.  准备MaxCompute的非分区表数据。

    在MaxCompute中创建一张非分区源数据表，或直接选用中已创建的非分区表。

    示例选取MaxCompute公共数据集**public\_data**的customer表，您可以参照[t12301.md\#section\_9hk\_a70\_qsg](/intl.zh-CN/公开数据集/概述.md)描述，登录并查询数据集。其表DDL以及数据如下。

    ```
    --MaxCompute公共数据集的表DDL
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
    
    --在MaxCompute中查询表是否有数据
    SELECT * FROM public_data.customer;
    ```

    部分数据内容显示如下图所示。

    ![customer](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5589793161/p242401.png)

2.  在Hologres中创建外部表。

    在Hologres中创建一张用于映射MaxCompute源数据表的外部表。示例SQL语句如下。

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

    |参数|描述|
    |--|--|
    |Server|您可以直接调用Hologres底层已创建的名为**odps\_server**的外部表服务器。详细原理请参见[Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k)。|
    |Project\_Name|MaxCompute表所在的项目名称。|
    |Table\_Name|需要查询的MaxCompute表名称。|

    外部表字段的数据类型与MaxCompute表字段的数据类型保持一致，数据类型的映射关系请参见[MaxCompute与Hologres的数据类型映射](/intl.zh-CN/SQL参考/数据类型/数据类型汇总.mdsection_w14_cec_th7)。

3.  在Hologres中创建存储表。

    在Hologres中创建一张用于接收MaxCompute源表数据的存储表。

    本示例是初步的DDL示例，实际导入数据时创建表请根据业务情况设置表结构并给表设置合适的索引，以达到更优的查询性能，更多关于表属性的描述，请参见[CREATE TABLE](/intl.zh-CN/SQL参考/DDL/TABLE/CREATE TABLE.md)。

    ```
    --示例建一张列存表
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

4.  导入数据至Hologres。

    使用`INSERT`语句将MaxCompute源头表中的数据导入至Hologres，您可以选择部分字段导入（字段顺序需要一一对应），也可以选择全部字段导入，示例DDL如下。

    ```
    --全部字段数据导入
    INSERT INTO holo_customer
    SELECT * FROM foreign_customer;
    
    --部分字段数据导入
    INSERT INTO holo_customer
    SELECT 
        "c_customer_sk",
        "c_customer_id",
        "c_email_address",
        "c_last_review_date",
        "useless"
    FROM foreign_customer;
    ```

5.  Hologres查询MaxCompute表数据。

    在Hologres中查询导入的MaxCompute表数据。示例SQL语句如下。

    ```
    SELECT * FROM holo_customer;
    ```


## 导入MaxCompute的分区表数据至Hologres并查询

1.  准备MaxCompute的分区表数据。

    在MaxCompute中创建一张分区数据表，详情请参见[分区和列操作](/intl.zh-CN/开发/SQL及函数/DDL语句/分区和列操作.md)。您也可以选取已创建的分区表。

    示例选取MaxCompute公共数据集**public\_data**的ods\_enterprise\_share\_trade\_h表，您可以参照[t12301.md\#section\_9hk\_a70\_qsg](/intl.zh-CN/公开数据集/概述.md)描述，登录并查询数据集。其表DDL以及数据如下。

    ```
    --公共数据集下表的DDL
    CREATE TABLE IF NOT EXISTS public_data.ods_enterprise_share_trade_h(
      trde_date STRING COMMENT '日期',
      open STRING COMMENT '开盘价',
      high STRING COMMENT '最高价',
      close STRING COMMENT '收盘价',
      low STRING COMMENT '最低价',
      volume STRING COMMENT '成交量',
      price_change STRING COMMENT '价格变动',
      p_change STRING COMMENT '涨跌幅',
      ma5 STRING COMMENT '5日均价',
      ma10 STRING COMMENT '10日均价',
      ma20 STRING COMMENT '20日均价',
      v_ma5 STRING COMMENT '5日均量',
      v_ma10 STRING COMMENT '10日均量',
      v_ma20 STRING COMMENT '20日均量',
      turnover STRING COMMENT '换手率',
      code STRING COMMENT '股票代码') 
      PARTITIONED BY (ds STRING) STORED AS ALIORC TBLPROPERTIES ('comment'='股票历史交易信息');
      
      --查询某个分区的数据
      SELECT * FROM public_data.ods_enterprise_share_trade_h WHERE ds = '20170113';
    ```

    部分数据内容显示如下图所示。

    ![trade](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5589793161/p242410.png)

2.  在Hologres创建外部表。

    在Hologres中创建一张用于映射MaxCompute源数据表的外部表。示例DDL如下。

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
    comment on foreign table public.foreign_ods_enterprise_share_trade_h is '股票历史交易信息';
    comment on column public.foreign_ods_enterprise_share_trade_h."trde_date" is '日期';
    comment on column public.foreign_ods_enterprise_share_trade_h."open" is '开盘价';
    comment on column public.foreign_ods_enterprise_share_trade_h."high" is '最高价';
    comment on column public.foreign_ods_enterprise_share_trade_h."close" is '收盘价';
    comment on column public.foreign_ods_enterprise_share_trade_h."low" is '最低价';
    comment on column public.foreign_ods_enterprise_share_trade_h."volume" is '成交量';
    comment on column public.foreign_ods_enterprise_share_trade_h."price_change" is '价格变动';
    comment on column public.foreign_ods_enterprise_share_trade_h."p_change" is '涨跌幅';
    comment on column public.foreign_ods_enterprise_share_trade_h."ma5" is '5日均价';
    comment on column public.foreign_ods_enterprise_share_trade_h."ma10" is '10日均价';
    comment on column public.foreign_ods_enterprise_share_trade_h."ma20" is '20日均价';
    comment on column public.foreign_ods_enterprise_share_trade_h."v_ma5" is '5日均量';
    comment on column public.foreign_ods_enterprise_share_trade_h."v_ma10" is '10日均量';
    comment on column public.foreign_ods_enterprise_share_trade_h."v_ma20" is '20日均量';
    comment on column public.foreign_ods_enterprise_share_trade_h."turnover" is '换手率';
    comment on column public.foreign_ods_enterprise_share_trade_h."code" is '股票代码';
    ```

3.  在Hologres创建存储表。

    在在Hologres中建立一张分区表，用于存储MaxCompute源头数据表中导入的数据。示例分区父表DDL如下。

    本示例是初步的DDL示例，实际导入数据时创建表请根据业务情况设置表结构并给表设置合适的索引，以达到更优的查询性能，更多关于表属性的描述，请参见[CREATE TABLE](/intl.zh-CN/SQL参考/DDL/TABLE/CREATE TABLE.md)。

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
    comment on column public.holo_ods_enterprise_share_trade_h.trde_date is '日期';
    comment on column public.holo_ods_enterprise_share_trade_h.open is '开盘价';
    comment on column public.holo_ods_enterprise_share_trade_h.high is '最高价';
    comment on column public.holo_ods_enterprise_share_trade_h.close is '收盘价';
    comment on column public.holo_ods_enterprise_share_trade_h.low is '最低价';
    comment on column public.holo_ods_enterprise_share_trade_h.volume is '成交量';
    comment on column public.holo_ods_enterprise_share_trade_h.price_change is '价格变动';
    comment on column public.holo_ods_enterprise_share_trade_h.p_change is '涨跌幅';
    comment on column public.holo_ods_enterprise_share_trade_h.ma5 is '5日均价';
    comment on column public.holo_ods_enterprise_share_trade_h.ma10 is '10日均价';
    comment on column public.holo_ods_enterprise_share_trade_h.ma20 is '20日均价';
    comment on column public.holo_ods_enterprise_share_trade_h.v_ma5 is '5日均量';
    comment on column public.holo_ods_enterprise_share_trade_h.v_ma10 is '10日均量';
    comment on column public.holo_ods_enterprise_share_trade_h.v_ma20 is '20日均量';
    comment on column public.holo_ods_enterprise_share_trade_h.turnover is '换手率';
    comment on column public.holo_ods_enterprise_share_trade_h.code is '股票代码';
    COMMIT;
    ```

    **说明：** ：Hologres不支持多级分区。MaxCompute的多级分区表数据可以导入至Hologres的非分区表或者一级分区表。

4.  创建子分区。

    创建完分区父表之后，还需要创建对应的分区子表，这样MaxCompute中的分区数据才能导入至指定的Hologres分区。示例DDL如下。

    一个分区数据对应一个分区子表，若是以日期分区，分区子表的命名建议带上分区时间，以便更好的区分分区表。

    ```
    --创建以20170113为分区的分区子表
    CREATE TABLE IF NOT EXISTS "public".holo_ods_enterprise_share_trade_h_20170113 PARTITION OF "public".holo_ods_enterprise_share_trade_h FOR VALUES IN ('20170113');
    
    --创建以20170114为分区的分区子表
    CREATE TABLE IF NOT EXISTS "public".holo_ods_enterprise_share_trade_h_20170114 PARTITION OF "public".holo_ods_enterprise_share_trade_h FOR VALUES IN ('20170114');
    ```

5.  导入数据至Hologres。

    当前暂不支持导入分区父表，需要将MaxCompute的分区数据导入至分区子表，同时分区数据必须和分区子表的分区键值一一对应。示例如下。

    ```
    --导入20170113的数据至分区键值为20170113的子表
    INSERT INTO "public".holo_ods_enterprise_share_trade_h_20170113
    SELECT *
    FROM "public".foreign_ods_enterprise_share_trade_h
    WHERE ds='20170113';
    
    --导入20170114的数据至分区键值为20170114的子表
    INSERT INTO "public".holo_ods_enterprise_share_trade_h_20170114
    SELECT *
    FROM "public".foreign_ods_enterprise_share_trade_h
    WHERE ds='20170114';
    ```

6.  查询表数据。

    数据导入成功之后，可以在Hologres中查询数据。您可以查询分区父表，也可以查分区子表。示例如下。

    ```
    --查询分区父表
    SELECT COUNT (*) FROM holo_ods_enterprise_share_trade_h;
    
    --查询分区子表
    SELECT trde_date FROM holo_ods_enterprise_share_trade_h_20170113;
    ```


## 通过可视化工具或周期性调度同步数据

如果您需要一次性同步大数据量，可以通过可视化工具或者调度任务来实现。

-   Holoweb一键同步表数据：通过Holoweb可以实现一键同步MaxCompute数据，无需SQL语句就能实现数据同步，但是Holoweb无法配置调度任务。更多关于Holoweb的操作，请参见[一键同步MaxCompute数据](/intl.zh-CN/连接开发工具/HoloWeb/连接管理/MaxCompute加速/一键同步MaxCompute数据.md)。
-   DataWorks调度任务同步：通过DataWorks实现周期性的MaxCompute数据导入，请参见[Hologres开发：周期性调度]()。

