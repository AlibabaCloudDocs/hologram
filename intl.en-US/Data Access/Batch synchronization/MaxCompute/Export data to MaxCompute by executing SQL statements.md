---
keyword: [Hologres, MaxCompute]
---

# Export data to MaxCompute by executing SQL statements

This topic shows you how to export data to MaxCompute by executing SQL statements in Hologres.

## Prerequisites

-   MaxCompute is activated. A development tool is connected to MaxCompute.
-   A Hologres instance is purchased. A development tool is connected to the instance. For more information, see [HoloWeb quick start](/intl.en-US/Quick Start/HoloWeb quick start.md).

## Notes

Take note of the following rules when you export data to MaxCompute by executing SQL statements in Hologres:

-   You can export data to MaxCompute by executing SQL statements only when you use an instance of Hologres V0.9 or later. To upgrade an instance, [submit a ticket](https://workorder-intl.console.aliyun.com/) or join the Hologres DingTalk group for technical support.
-   Hologres allows you to export data to MaxCompute across regions. However, if the destination MaxCompute project resides in the same region as your Hologres instance, the performance is better due to the stable network environment.
-   Hologres supports a maximum of one level of partitions but allows you to export data to the subpartitions of MaxCompute tables. To export data to the subpartitions of a MaxCompute table, you must map the partition key values of the MaxCompute table to the fields in the source Hologres table. In addition, Hologres allows you to export data from a partitioned Hologres table to a non-partitioned MaxCompute table.
-   For more information about data type mappings between Hologres and MaxCompute, see [Data types](/intl.en-US/Hologres SQL/Data types/Data types.md).
-   You cannot export data of the ARRAY, MAP, or STRUCT type from Hologres to MaxCompute.

## Procedure

1.  In Hologres, create an internal table whose data is to be exported to MaxCompute. For example, you can create a table named holo\_source.
2.  Create a MaxCompute table to receive data. For example, you can create a table named mc\_sink.
3.  In Hologres, create a foreign table to map the data of the internal table to MaxCompute. For example, you can create a table named mapping\_foreign\_table.
4.  In Hologres, execute the following SQL statements as required to export data to the MaxCompute table:

    ```
    // Export the data of the specified fields.
    insert into mapping_foreign_table
    select x,x,x from holo_soruce; // Replace x,x,x with the names of the fields whose data you want to export.
    
    // Export the data of all fields.
    insert into mapping_foreign_table
    select * from holo_soruce;
    ```


The following part describes how to synchronize data between non-partitioned tables and between partitioned tables.

## Export data from a non-partitioned Hologres table to a non-partitioned MaxCompute table

1.  Create an internal table in Hologres as the data source.

    Execute the following statements to create the internal table whose data is to be exported to MaxCompute:

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

2.  Create a MaxCompute table to receive data.

    Execute the following statement to create the destination MaxCompute table. Specify the fields and field types based on the internal table. For more information about the syntax, see [Table operations](/intl.en-US/Development/SQL/DDL SQL/Table operations.md).

    ```
    CREATE TABLE IF NOT EXISTS mc_bank
    (
     age             BIGINT COMMENT 'Age',
     job             STRING COMMENT 'Job type',
     marital         STRING COMMENT 'Marital status',
     education       STRING COMMENT 'Education level',
     card         STRING COMMENT 'Credit card available or not',
     housing         STRING COMMENT 'Mortgage',
     loan            STRING COMMENT 'Loan',
     contact         STRING COMMENT 'Contact',
     month           STRING COMMENT 'Month',
     day_of_week     STRING COMMENT 'Day of the week',
     duration        STRING COMMENT 'Duration',
     campaign        BIGINT COMMENT 'Number of contacts during the campaign',
     pdays           DOUBLE COMMENT 'Interval from the last contact',
     previous        DOUBLE COMMENT 'Number of contacts with the customer',
     poutcome        STRING COMMENT 'Result of the previous marketing campaign',
     emp_var_rate    DOUBLE COMMENT 'Employment change rate',
     cons_price_idx  DOUBLE COMMENT 'Consumer price index',
     cons_conf_idx   DOUBLE COMMENT 'Consumer confidence index',
     euribor3m       DOUBLE COMMENT 'Euro deposit rate',
     nr_employed     DOUBLE COMMENT 'Number of employees',
     y               BIGINT COMMENT 'Time deposit available or not'
    );
    ```

3.  Create a foreign table in Hologres to map the data of the internal table to MaxCompute.

    Execute the following statements to create the foreign table that is used to map data. Alternatively, you can import a MaxCompute table as a foreign table.

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
    OPTIONS (project_name 'xxx',table_name 'mc_bank'); // Set the project_name parameter to the name of the destination MaxCompute project and the table_name parameter to the name of the destination MaxCompute table.
    COMMIT;
    ```

4.  Export data from Hologres to MaxCompute.

    Execute the following statements as required to export data to the destination MaxCompute table. You can determine whether to export the data of the specified fields or all the fields. You must specify fields in the same sequence as they are positioned in the internal table.

    ```
    // Export the data of the specified fields.
    set hg_experimental_enable_write_maxcompute = on; // You must use a Grand Unified Configuration (GUC) parameter because this data export feature is in a beta version.
    insert into mapping_bank 
    select age,job from bank;
    
    // Export the data of all fields.
    set hg_experimental_enable_write_maxcompute = on; // You must use a GUC parameter because this data export feature is in a beta version.
    insert into mapping_bank 
    select *from bank;
                            
    ```


## Export data from a partitioned Hologres table to a partitioned MaxCompute table

1.  Create an internal table in Hologres as the data source.

    Execute the following statements to create the internal table whose data is to be exported to MaxCompute:

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
    
    // Child partitioned tables are required.
    CREATE TABLE "public"."par_bank_20190830" PARTITION OF "public"."par_bank" FOR VALUES IN ('20190830');
    
    CREATE TABLE "public"."par_bank_20190901" PARTITION OF "public"."par_bank" FOR VALUES IN ('20190901');
    ```

2.  Create a MaxCompute table to receive data.

    Execute the following statements as required to create the destination MaxCompute table. The MaxCompute table can contain one or two levels of partitions. Specify the fields and field types based on the internal table. For more information about the syntax, see [Table operations](/intl.en-US/Development/SQL/DDL SQL/Table operations.md).

    ```
    // Case 1: Create a MaxCompute table that contains only one level of partitions.
    CREATE TABLE IF NOT EXISTS mc_par_bank
    (
        age            BIGINT COMMENT 'Age',
        job            STRING COMMENT 'Job type',
        marital        STRING COMMENT 'Marital status',
        education      STRING COMMENT 'Education level',
        default         STRING COMMENT 'Credit card available or not',
        housing        STRING COMMENT 'Mortgage',
        loan           STRING COMMENT 'Loan',
        contact        STRING COMMENT 'Contact',
        month          STRING COMMENT 'Month',
        day_of_week    STRING COMMENT 'Day of the week',
        duration       STRING COMMENT 'Duration',
        campaign       BIGINT COMMENT 'Number of contacts during the campaign',
        pdays          DOUBLE COMMENT 'Interval from the last contact',
        previous       DOUBLE COMMENT 'Number of contacts with the customer',
        poutcome       STRING COMMENT 'Result of the previous marketing campaign',
        emp_var_rate   DOUBLE COMMENT 'Employment change rate',
        cons_price_idx DOUBLE COMMENT 'Consumer price index',
        cons_conf_idx  DOUBLE COMMENT 'Consumer confidence index',
        euribor3m      DOUBLE COMMENT 'Euro deposit rate',
        nr_employed    DOUBLE COMMENT 'Number of employees',
        y              BIGINT COMMENT 'Time deposit available or not'
    ) 
    PARTITIONED BY
    (
        ds             STRING
    );
    // Create the required partitions if they do not exist.
    alter table mc_par_bank add if not exists partition (ds='20190830');
    alter table mc_par_bank add if not exists partition (ds='20190901');
    
    
    // Case 2: Create a MaxCompute table that contains two levels of partitions.
    CREATE TABLE IF NOT EXISTS mc_par_bank_2
    (
        age            BIGINT COMMENT 'Age',
        job            STRING COMMENT 'Job type',
        marital        STRING COMMENT 'Marital status',
        education      STRING COMMENT 'Education level',
        default         STRING COMMENT 'Credit card available or not',
        housing        STRING COMMENT 'Mortgage',
        loan           STRING COMMENT 'Loan',
        contact        STRING COMMENT 'Contact',
        month          STRING COMMENT 'Month',
        day_of_week    STRING COMMENT 'Day of the week',
        duration       STRING COMMENT 'Duration',
        campaign       BIGINT COMMENT 'Number of contacts during the campaign',
        pdays          DOUBLE COMMENT 'Interval from the last contact',
        previous       DOUBLE COMMENT 'Number of contacts with the customer',
        poutcome       STRING COMMENT 'Result of the previous marketing campaign',
        emp_var_rate   DOUBLE COMMENT 'Employment change rate',
        cons_price_idx DOUBLE COMMENT 'Consumer price index',
        cons_conf_idx  DOUBLE COMMENT 'Consumer confidence index',
        euribor3m      DOUBLE COMMENT 'Euro deposit rate',
        nr_employed    DOUBLE COMMENT 'Number of employees',
    ) 
    PARTITIONED BY
    (
        y              BIGINT, 
        ds             STRING
    );
    
    alter table mc_par_bank_2 add if not exists partition (y='1',ds='20190830');
    alter table mc_par_bank_2 add if not exists partition (y='1',ds='20190901');
    ```

3.  Create a foreign table in Hologres to map the data of the internal table to MaxCompute.

    Execute the following statements as required to create the foreign table that is used to map data:

    ```
    // Create a foreign table to map data to the MaxCompute table that contains only one level of partitions.
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
    
    
    // Create a foreign table to map data to the MaxCompute table that contains two levels of partitions.
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
    OPTIONS (project_name 'xxx',table_name 'mc_par_bank_2'); // Set the project_name parameter to the name of the destination MaxCompute project and the table_name parameter to the name of the destination MaxCompute table.
    COMMIT;
                            
    ```

4.  Export data from Hologres to MaxCompute.

    Execute the following statements as required to export data to the destination MaxCompute table. You can determine whether to export the data of the specified fields or all the fields. You must specify fields in the same sequence as they are positioned in the internal table.

    ```
    // Export data to the MaxCompute table that contains only one level of partitions.
    // Example 1: Export data from the parent partitioned table in Hologres. Use a WHERE clause to restrict the data to be affected.
    set hg_experimental_enable_write_maxcompute = on;-- You must use a GUC parameter because this data export feature is in a beta version.
    
    INSERT INTO mapping_par_bank SELECT * FROM "public"."par_bank" WHERE ds='20190830';
    
    // Example 2: Export data from the corresponding child partitioned table in Hologres.
    set hg_experimental_enable_write_maxcompute = on;-- You must use a GUC parameter because this data export feature is in a beta version.
    
    insert into mapping_par_bank select * from "public"."par_bank_20190901";
    
    
    // Export data to the MaxCompute table that contains two levels of partitions.
    // Example 1: Export data from the parent partitioned table in Hologres. Use a WHERE clause to restrict the data to be affected.
    set hg_experimental_enable_write_maxcompute = on;
    
    INSERT INTO mapping_par_bank_2 SELECT * FROM "public"."par_bank" WHERE y='1' and ds='20190830';
    
    // Example 2: Export data from the corresponding child partitioned table in Hologres.
    set hg_experimental_enable_write_maxcompute = on;
    
    INSERT INTO mapping_par_bank_2 SELECT * FROM "public"."par_bank_20190901" where y='1';
    ```


