# Auto Analyze

本文将为你介绍Hologres中Auto Analyze的相关机制。

## 功能概述

Analyze命令用于收集数据库中表内容的统计信息，优化器会根据这些统计信息生成最佳的查询计划，从而提高查询效率。

从Hologres V0.10版本开始，将支持Auto Analyze机制，无需再手动对表进行Analyze。您可以通过命令语句开启Auto Analyze，系统会根据用户的数据写入、修改或者查询的情况来判断是否需要对相关的表自动进行Analyze，降低操作复杂度。

## 使用限制

在Hologres中使用Auto Analyze，具体限制如下：

-   该功能仅Hologres V0.10及以上版本支持，请在Hologres管理控制台的实例详情页查看当前版本，如果您的实例是V0.10以下版本，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)升级实例。
-   仅支持Superuser执行开启或关闭Auto Analyze操作。
-   开启或关闭Auto Analyze的操作仅针对数据库级别生效，不针对单表。

## 开启Auto Analyze

实例的Superuser可以通过如下命令开启Auto Analyze。

-   当前DB开启Auto Analyze。

    ```
    --将<databasename>替换为实际DB名称，执行后整个DB生效
    alter database <databasename> set hg_experimental_enable_start_auto_analyze_worker = on;
    ```

-   当前session开启Auto Analyze。

    ```
    --session级别，执行后只对当前连接生效
    set hg_experimental_enable_start_auto_analyze_worker = on;
    ```


以下几种情况会触发Auto Analyze机制，Superuser也可以根据业务情况更改默认参数，以此达到部分性能调优的目的。更多关于触发机制的说明和后台参数配置，请参见[配置参数](#section_3kc_lvh_ttg)。

-   DML语句执行完成。
-   新建一个表。
-   外部表最后更新时间发生了改变，会触发外部表的Auto Analyze。
-   后台默认设置检查机制进行Auto Analyze，具体机制如下：
    -   内部表默认每10分钟执行一次Auto Analyze。
    -   外部表默认每4小时执行一次Auto Analyze。
    -   扫描表的最大记录数是224条（16,777,216条），如果表的记录条数超过224条，将不再对超过的记录数自动进行Auto Analyze。
    -   表超过3天无Flink或Blink实时写入，则不会对表进行Auto Analyze。

## 关闭Auto Analyze

当您的业务不需要Auto Analyze时，例如查询的query比较固定，实例的Superuser可以通过如下命令关闭Auto Analyze。

-   当前DB关闭Auto Analyze。

    ```
    --将<databasename>替换为实际DB名称，执行后整个DB生效
    alter database <databasename> set hg_experimental_enable_start_auto_analyze_worker = off;
    ```

-   当前session关闭Auto Analyze。

    ```
    --session级别，执行后只对当前连接生效
    set hg_experimental_enable_start_auto_analyze_worker = off;
    ```


## 配置参数

当您开启了Auto Analyze后，系统会根据默认设定的时间和机制进行巡检并执行Auto Analyze，会消耗一定的资源。在某些业务场景下，默认的机制可能会不适用业务场景，例如数据写入更新不频繁场景，可以通过修改默认参数来减少Auto Analyze的频率。

Superuser可以根据业务情况更改默认参数，以此达到部分性能调优的目的。

-   修改周期性检查所有内部表的更新时间间隔，内部表默认每10分钟执行一次Auto Analyze。

    ```
    --session级别，只对当前连接生效。
    set hg_experimental_auto_check_table_changes_interval = 10min;
    
    --DB级别，执行后整个DB生效，将<databasename>替换为实际的DB名称。
    alter database <databasename> set hg_experimental_auto_check_table_changes_interval = 10min;
    ```

-   修改周期性检查所有外表的更新时间间隔，外部表默认每4小时执行一次Auto Analyze。

    ```
    --session级别，只对当前连接生效。
    set hg_experimental_auto_check_foreign_table_changes_interval = 240min;
    
    --DB级别，执行后整个DB生效，将<databasename>替换为实际的DB名称。
    alter database <databasename> set hg_experimental_auto_check_foreign_table_changes_interval = 240min;
    ```

-   修改扫描表的最大记录数，扫描表的最大记录数默认是224条（16,777,216条），如果表的记录条数超过224条，将不再对超过的记录数自动进行Auto Analyze。

    **说明：** 对于分区表，224条是指整个父表的记录条数。

    ```
    --session级别，只对当前连接生效。
    set hg_experimental_auto_analyze_max_sample_row_count = 1 << 24;
    
    --DB级别，执行后整个DB生效，将<databasename>替换为实际的DB名称。
    alter database <databasename> set hg_experimental_auto_analyze_max_sample_row_count = 1 << 24;
    ```

-   修改Hologres内部表Flink或Blink写入的最大延迟，默认表超过3天无Flink或Blink实时写入，则不会对表进行Auto Analyze。

    ```
    --session级别，只对当前连接生效。
    set hg_experimental_fixed_api_modify_max_delay_interval = 3day;
    
    --DB级别，执行后整个DB生效，将<databasename>替换为实际的DB名称。
    alter database <databasename> set hg_experimental_fixed_api_modify_max_delay_interval = 3day;
    ```


