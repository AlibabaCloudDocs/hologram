---
keyword: [Hologres, MaxCompute]
---

# 通过SQL导出数据至MaxCompute（Beta）

本文将为您介绍如何在Hologres中通过SQL方式将数据导出至MaxCompute。

## 前提条件

-   开通MaxCompute并连接开发工具，请参见[快速体验MaxCompute](/cn.zh-CN/快速入门/快速体验MaxCompute.md)。
-   开通Hologres并连接开发工具，请参见[HoloWeb快速入门](/cn.zh-CN/快速入门/HoloWeb快速入门.md)。

## 注意事项

在Hologres中使用SQL导出数据至MaxCompute需要注意如下事项：

-   仅Hologres V0.9及以上版本支持使用SQL导出数据至MaxCompute，如果您的实例是V0.9以下版本，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)或加入在线支持钉钉群申请升级实例。
-   支持跨区域导出至MaxCompute，但因为网络原因，同一个区域导出的性能会更好。
-   当前Hologres仅支持一级分区，但是可以导出至MaxCompute的二级分区，但是需要MaxCompute的分区值与Hologres字段一一对应。同时Hologres的分区表也可以导入至MaxCompute的非分区表。
-   Hologres的数据类型与MaxCompute的数据类型一一映射，请参见[数据类型汇总](/cn.zh-CN/Hologres SQL/数据类型/数据类型汇总.md)。
-   目前Hologres不支持将ARRAY、MAP、STRUCT等复杂数据类型导出至MaxCompute中。

## 操作流程

1.  在Hologres准备一张Hologres内部表（例如：holo\_source），用于导出数据至MaxCompute。
2.  在MaxCompute准备一张MaxComute表用于接收数据（例如mc\_sink）。
3.  在Hologres新建一张外部表，用于映射Hologres内部表（例如 mapping\_foreign\_table）。
4.  在Hologres通过SQL语句导出数据至MaxCompute。

    ```
    //导出部分字段
    insert into mapping_foreign_table
    select x,x,x from holo_soruce;//x,x,x可以替换为您需要到处的字段名
    
    //导出全部字段
    insert into mapping_foreign_table
    select * from holo_soruce;
    ```


下面将会分非分区表和分区表两种情况来说明具体操作。

## 非分区表导出至MaxCompute非分区表

1.  准备Hologres数据源表。

    在Hologres中准备一张数据表，用于将数据导出至MaxCompute。示例选择已有表其DDL如下：

    ```
    BEGIN;
    CREATE  TABLE "public"."bank" (
     "age" int8,
     "job" text,
     "marital" text,
     "education" text,
     "card" text,
     "housing" text,
     "loan" text,
     "contact" text,
     "month" text,
     "day_of_week" text,
     "duration" text,
     "campaign" int8,
     "pdays" float8,
     "previous" float8,
     "poutcome" text,
     "emp_var_rate" float8,
     "cons_price_idx" float8,
     "cons_conf_idx" float8,
     "euribor3m" float8,
     "nr_employed" float8,
     "y" int8
    );
    COMMIT;
    ```

2.  创建MaxCompute目标表。

    在MaxCompute中创建一张目标表，用于接收数据，字段顺序与字段类型需要与Hologers表一一映射。其建表语法，请参见[表操作](/cn.zh-CN/开发/SQL及函数/DDL语句/表操作.md)。

    ```
    CREATE TABLE IF NOT EXISTS mc_bank
    (
     age             BIGINT COMMENT '年龄',
     job             STRING COMMENT '工作类型',
     marital         STRING COMMENT '婚否',
     education       STRING COMMENT '教育程度',
     card            STRING COMMENT '是否有信用卡',
     housing         STRING COMMENT '房贷',
     loan            STRING COMMENT '贷款',
     contact         STRING COMMENT '联系途径',
     month           STRING COMMENT '月份',
     day_of_week     STRING COMMENT '星期几',
     duration        STRING COMMENT '持续时间',
     campaign        BIGINT COMMENT '本次活动联系的次数',
     pdays           DOUBLE COMMENT '与上一次联系的时间间隔',
     previous        DOUBLE COMMENT '之前与客户联系的次数',
     poutcome        STRING COMMENT '之前市场活动的结果',
     emp_var_rate    DOUBLE COMMENT '就业变化速率',
     cons_price_idx  DOUBLE COMMENT '消费者物价指数',
     cons_conf_idx   DOUBLE COMMENT '消费者信心指数',
     euribor3m       DOUBLE COMMENT '欧元存款利率',
     nr_employed     DOUBLE COMMENT '职工人数',
     y               BIGINT COMMENT '是否有定期存款'
    );
    ```

3.  准备一张映射到MaxCompute的Hologres外部表。

    在Hologres中准备一张外表，用于将字段映射至MaxCompute，也可以通过Import Foreign Table导入MaxCompute外表。创建外表示例DDL如下：

    ```
    BEGIN;
    CREATE FOREIGN TABLE "public"."mapping_bank" (
     "age" int8,
     "job" text,
     "marital" text,
     "education" text,
     "card" text,
     "housing" text,
     "loan" text,
     "contact" text,
     "month" text,
     "day_of_week" text,
     "duration" text,
     "campaign" int8,
     "pdays" float8,
     "previous" float8,
     "poutcome" text,
     "emp_var_rate" float8,
     "cons_price_idx" float8,
     "cons_conf_idx" float8,
     "euribor3m" float8,
     "nr_employed" float8,
     "y" int8
    )
    SERVER odps_server
    OPTIONS (project_name 'xxx',table_name 'mc_bank');//project_name为MaxCompute的project名，table_name为MaxCompute接收数据的表名
    COMMIT;
    ```

4.  导出Hologres数据至MaxCompute。

    在Hologres中执行SQL语句将数据导出至MaxCompute，您可以选择导出部分字段，也可以选择导出全部字段，导出部分字段时需要保证字段顺序一致。

    ```
    //导出部分字段数据
    set hg_experimental_enable_write_maxcompute = on;//由于是Beta功能，需要打开一个GUC参数
    insert into mapping_bank 
    select age,job from bank;
    
    //导出全部字段数据
    set hg_experimental_enable_write_maxcompute = on;//由于是Beta功能，需要打开一个GUC参数
    insert into mapping_bank 
    select *from bank;
                            
    ```


## 分区表导出至MaxCompute分区表

1.  准备Hologres数据源表。

    在Hologres中准备一张分区源表，用于将数据导出至MaxCompute。示例选择已有表其DDL如下：

    ```
    BEGIN;
    CREATE TABLE "public"."par_bank" (
     "age" int8,
     "job" text,
     "marital" text,
     "education" text,
     "default" text,
     "housing" text,
     "loan" text,
     "contact" text,
     "month" text,
     "day_of_week" text,
     "duration" text,
     "campaign" int8,
     "pdays" float8,
     "previous" float8,
     "poutcome" text,
     "emp_var_rate" float8,
     "cons_price_idx" float8,
     "cons_conf_idx" float8,
     "euribor3m" float8,
     "nr_employed" float8,
     "y" int8,
     "ds" text
    )
    PARTITION BY list (ds);
    COMMIT;
    
    //需要有分区子表
    CREATE TABLE "public"."par_bank_20190830" PARTITION OF "public"."par_bank" FOR VALUES IN ('20190830');
    
    CREATE TABLE "public"."par_bank_20190901" PARTITION OF "public"."par_bank" FOR VALUES IN ('20190901');
    ```

2.  创建MaxCompute目标表。

    在MaxCompute中创建一张目标表，用于接收数据，表可以是一级分区，也可以是二级分区。字段顺序与字段类型需要与Hologers表一一映射。其建表语法，请参见[表操作](/cn.zh-CN/开发/SQL及函数/DDL语句/表操作.md)。

    ```
    //情况1:MaxCompute表是一级分区
    CREATE TABLE IF NOT EXISTS mc_par_bank
    (
        age            BIGINT COMMENT '年龄',
        job            STRING COMMENT '工作类型',
        marital        STRING COMMENT '婚否',
        education      STRING COMMENT '教育程度',
        default        STRING COMMENT '是否有信用卡',
        housing        STRING COMMENT '房贷',
        loan           STRING COMMENT '贷款',
        contact        STRING COMMENT '联系途径',
        month          STRING COMMENT '月份',
        day_of_week    STRING COMMENT '星期几',
        duration       STRING COMMENT '持续时间',
        campaign       BIGINT COMMENT '本次活动联系的次数',
        pdays          DOUBLE COMMENT '与上一次联系的时间间隔',
        previous       DOUBLE COMMENT '之前与客户联系的次数',
        poutcome       STRING COMMENT '之前市场活动的结果',
        emp_var_rate   DOUBLE COMMENT '就业变化速率',
        cons_price_idx DOUBLE COMMENT '消费者物价指数',
        cons_conf_idx  DOUBLE COMMENT '消费者信心指数',
        euribor3m      DOUBLE COMMENT '欧元存款利率',
        nr_employed    DOUBLE COMMENT '职工人数',
        y              BIGINT COMMENT '是否有定期存款'
    ) 
    PARTITIONED BY
    (
        ds             STRING
    );
    //为一级分区添加分区
    alter table mc_par_bank add if not exists partition (ds='20190830');
    alter table mc_par_bank add if not exists partition (ds='20190901');
    
    
    //情况二：MaxCompute表是二级分区
    CREATE TABLE IF NOT EXISTS mc_par_bank_2
    (
        age            BIGINT COMMENT '年龄',
        job            STRING COMMENT '工作类型',
        marital        STRING COMMENT '婚否',
        education      STRING COMMENT '教育程度',
        default        STRING COMMENT '是否有信用卡',
        housing        STRING COMMENT '房贷',
        loan           STRING COMMENT '贷款',
        contact        STRING COMMENT '联系途径',
        month          STRING COMMENT '月份',
        day_of_week    STRING COMMENT '星期几',
        duration       STRING COMMENT '持续时间',
        campaign       BIGINT COMMENT '本次活动联系的次数',
        pdays          DOUBLE COMMENT '与上一次联系的时间间隔',
        previous       DOUBLE COMMENT '之前与客户联系的次数',
        poutcome       STRING COMMENT '之前市场活动的结果',
        emp_var_rate   DOUBLE COMMENT '就业变化速率',
        cons_price_idx DOUBLE COMMENT '消费者物价指数',
        cons_conf_idx  DOUBLE COMMENT '消费者信心指数',
        euribor3m      DOUBLE COMMENT '欧元存款利率',
        nr_employed    DOUBLE COMMENT '职工人数'
    ) 
    PARTITIONED BY
    (
        y              BIGINT, 
        ds             STRING
    );
    
    alter table mc_par_bank_2 add if not exists partition (y='1',ds='20190830');
    alter table mc_par_bank_2 add if not exists partition (y='1',ds='20190901');
    ```

3.  准备一张映射到MaxCompute的Hologres外部表。

    在Hologres中准备一张外表，用于将字段映射至MaxCompute。创建外表示例DDL如下：

    ```
    //映射MaxCompute的一级分区表
    BEGIN;
    CREATE FOREIGN TABLE "public"."mapping_par_bank" (
     "age" int8,
     "job" text,
     "marital" text,
     "education" text,
     "default" text,
     "housing" text,
     "loan" text,
     "contact" text,
     "month" text,
     "day_of_week" text,
     "duration" text,
     "campaign" int8,
     "pdays" float8,
     "previous" float8,
     "poutcome" text,
     "emp_var_rate" float8,
     "cons_price_idx" float8,
     "cons_conf_idx" float8,
     "euribor3m" float8,
     "nr_employed" float8,
     "y" int8,
     "ds" text
    )
    SERVER odps_server
    OPTIONS (project_name 'xxx',table_name 'mc_par_bank');
    COMMIT;
    
    
    //映射MaxCompute的二级分区表
    BEGIN;
    CREATE FOREIGN TABLE "public"."mapping_par_bank_2" (
     "age" int8,
     "job" text,
     "marital" text,
     "education" text,
     "default" text,
     "housing" text,
     "loan" text,
     "contact" text,
     "month" text,
     "day_of_week" text,
     "duration" text,
     "campaign" int8,
     "pdays" float8,
     "previous" float8,
     "poutcome" text,
     "emp_var_rate" float8,
     "cons_price_idx" float8,
     "cons_conf_idx" float8,
     "euribor3m" float8,
     "nr_employed" float8,
     "y" int8,
     "ds" text
    )
    SERVER odps_server
    OPTIONS (project_name 'xxx',table_name 'mc_par_bank_2');//project_name为MaxCompute的project名，table_name为MaxCompute接收数据的表名
    COMMIT;
                            
    ```

4.  导出Hologres数据至MaxCompute。

    在Hologres中执行SQL语句将数据导出至MaxCompute，您可以选择导出部分字段，也可以选择导出全部字段，导出部分字段时需要保证字段顺序一致。

    ```
    //导出至MaxCompute一级分区表
    //示例1：加where条件的分区父表
    set hg_experimental_enable_write_maxcompute = on;-- 由于是beta功能，需要打开一个GUC参数
    
    INSERT INTO mapping_par_bank SELECT * FROM "public"."par_bank" WHERE ds='20190830';
    
    //示例2：直接通过分区子表导出
    set hg_experimental_enable_write_maxcompute = on;-- 由于是beta功能，需要打开一个GUC参数
    
    insert into mapping_par_bank select * from "public"."par_bank_20190901";
    
    
    //导出至MaxCompute二级分区表
    //示例1：加where条件的分区父表
    set hg_experimental_enable_write_maxcompute = on;
    
    INSERT INTO mapping_par_bank_2 SELECT * FROM "public"."par_bank" WHERE y='1' and ds='20190830';
    
    //示例2：直接通过Hologres分区子表导出
    set hg_experimental_enable_write_maxcompute = on;
    
    INSERT INTO mapping_par_bank_2 SELECT * FROM "public"."par_bank_20190901" where y='1';
    ```


