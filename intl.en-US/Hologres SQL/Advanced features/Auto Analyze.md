# Auto Analyze

This topic describes the Auto Analyze feature of Hologres.

## Overview

You can implement the Analyze feature to collect the statistics of table data in your database. Then, the query optimizer of Hologres generates an optimal query plan based on the statistics. This improves query efficiency.

Hologres V0.10 and later provide the Auto Analyze feature and do not require you to manually implement the Analyze feature. You can enable the Auto Analyze feature by executing specific SQL statements. After this feature is enabled, Hologres determines whether to automatically implement the Analyze feature on tables based on the data writing, modification, and query operations that you perform on the tables. This facilitates data queries.

## Limits

Take note of the following rules when you use the Auto Analyze feature of Hologres:

-   This feature is supported only by Hologres V0.10 and later. You can check the version of your Hologres instance on the instance details page in the Hologres console. If your instance version is earlier than V0.10, [submit a ticket](https://workorder-intl.console.aliyun.com/) to upgrade your instance.
-   To enable or disable the Auto Analyze feature, you must be a superuser.
-   The Auto Analyze feature takes effect at the database level, rather than the table level.

## Enable the Auto Analyze feature

If you are a superuser of a Hologres instance, you can enable the Auto Analyze feature in the following ways:

-   Execute the following statement to enable the Auto Analyze feature for the specified database:

    ```
    -- Replace <databasename> with the name of a database. The Auto Analyze feature is to be enabled for the database.
    alter database <databasename> set hg_experimental_enable_start_auto_analyze_worker = on;
    ```

-   Execute the following statement to enable the Auto Analyze feature for the current session:

    ```
    -- The Auto Analyze feature is to be enabled for the current session.
    set hg_experimental_enable_start_auto_analyze_worker = on;
    ```


The Auto Analyze feature is automatically implemented in the following scenarios. If you are a superuser, you can modify the default implementation settings by configuring the relevant parameters based on your business requirements. This allows you to optimize the feature performance in a fine-grained manner. For more information about how this feature is implemented and the parameters involved, see [Configure parameters](#section_3kc_lvh_ttg).

-   The execution of a data manipulation language \(DML\) statement is complete.
-   A table is created.
-   The data in a foreign table changes. In this case, the Auto Analyze feature is implemented on the foreign table.
-   The following list shows the default implementation setting of the Auto Analyze feature:
    -   The Auto Analyze feature is implemented every 10 minutes on internal tables.
    -   The Auto Analyze feature is implemented every 4 hours on foreign tables.
    -   A maximum of 224 \(16,777,216\) records can be scanned each time the Auto Analyze feature is implemented on a table. If the table contains more than 224 records, the Auto Analyze feature is not implemented on the excess records.
    -   If no data has been written to an internal table in real time from Blink-based Realtime Compute, Realtime Compute for Apache Flink, or Apache Flink for more than three days, the Auto Analyze feature is not implemented on the internal table.

## Disable the Auto Analyze feature

In specific business scenarios, such as the business scenario in which you have specified fixed query statements, your business may not require the Auto Analyze feature. In this case, if you are a superuser of a Hologres instance, you can disable the Auto Analyze feature in the following ways:

-   Execute the following statement to disable the Auto Analyze feature for the specified database:

    ```
    -- Replace <databasename> with the name of a database. The Auto Analyze feature is to be disabled for the database.
    alter database <databasename> set hg_experimental_enable_start_auto_analyze_worker = off;
    ```

-   Execute the following statement to disable the Auto Analyze feature for the current session:

    ```
    -- The Auto Analyze feature is to be disabled for the current session.
    set hg_experimental_enable_start_auto_analyze_worker = off;
    ```


## Configure parameters

After you enable the Auto Analyze feature, Hologres performs inspections and implements the feature based on the default implementation settings. This consumes resources. The default settings may not apply to specific business scenarios. For example, data may not be frequently written to your Hologres instance. In this case, you can decrease the implementation frequency of the Auto Analyze feature.

If you are a superuser, you can modify the default implementation settings by configuring the relevant parameters based on your business requirements. This allows you to optimize the feature performance in a fine-grained manner.

-   Execute one of the following statements to specify the intervals at which Hologres implements the Auto Analyze feature on internal tables. By default, Hologres implements the Auto Analyze feature every 10 minutes on all internal tables to check the data changes in these tables.

    ```
    -- The feature setting takes effect only on the current session. 
    set hg_experimental_auto_check_table_changes_interval = '10min';
    
    -- The feature setting takes effect on the specified database. Replace <databasename> with the name of a database. 
    alter database <databasename> set hg_experimental_auto_check_table_changes_interval = '10min';
    ```

-   Execute one of the following statements to specify the intervals at which Hologres implements the Auto Analyze feature on foreign tables. By default, Hologres implements the Auto Analyze feature every 4 hours on all foreign tables to check the data changes in these tables.

    ```
    -- The feature setting takes effect only on the current session. 
    set hg_experimental_auto_check_foreign_table_changes_interval = '240min';
    
    -- The feature setting takes effect on the specified database. Replace <databasename> with the name of a database. 
    alter database <databasename> set hg_experimental_auto_check_foreign_table_changes_interval = '240min';
    ```

-   Execute one of the following statements to specify the maximum number of records that can be scanned each time the Auto Analyze feature is implemented on a table. The default value is 224 \(16,777,216\). Under the default setting, if a table contains more than 224 records, the Auto Analyze feature is not implemented on the excess records.

    **Note:** For a partitioned table, the limit of 224 records in the default setting is imposed on the parent table.

    ```
    -- The feature setting takes effect only on the current session. 
    set hg_experimental_auto_analyze_max_sample_row_count = 1 << 24;
    
    -- The feature setting takes effect on the specified database. Replace <databasename> with the name of a database. 
    alter database <databasename> set hg_experimental_auto_analyze_max_sample_row_count = 1 << 24;
    ```

-   Execute one of the following statements to specify the maximum latency allowed for the data that is written from Blink-based Realtime Compute, Realtime Compute for Apache Flink, or Apache Flink to an internal table. By default, if no such data has been written to the internal table in real time for more than three days, the Auto Analyze feature is not implemented on the internal table.

    ```
    -- The feature setting takes effect only on the current session. 
    set hg_experimental_fixed_api_modify_max_delay_interval = 3day;
    
    -- The feature setting takes effect on the specified database. Replace <databasename> with the name of a database. 
    alter database <databasename> set hg_experimental_fixed_api_modify_max_delay_interval = 3day;
    ```


