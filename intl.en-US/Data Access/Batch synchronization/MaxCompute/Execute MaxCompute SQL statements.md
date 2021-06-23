# Execute MaxCompute SQL statements

In Hologres V0.10 and later, Hologres is further integrated with MaxCompute. You can execute MaxCompute SQL statements in Hologres. This allows you to manage MaxCompute data with ease. This topic describes how to execute MaxCompute SQL statements in Hologres.

## Prerequisites

-   Hologres is activated. A development tool is connected to your Hologres instance. In this example, HoloWeb is used. For more information, see [HoloWeb quick start](/intl.en-US/Quick Start/HoloWeb quick start.md).
-   MaxCompute is activated. For more information, see [Activate MaxCompute](/intl.en-US/Prepare/Activate MaxCompute.md).
-   You have permissions to execute SQL statements in MaxCompute. For more information about the permissions in MaxCompute, see [Management of users and permissions](/intl.en-US/Security Management/Security management/Management of users and permissions.md).

## Limits

-   Only Hologres V0.10 and later allow you to execute MaxCompute SQL statements in Hologres. You can view the version of your Hologres instance on the instance details page in the Hologres console. If the version of your Hologres instance is earlier than V0.10, [submit a ticket](https://workorder-intl.console.aliyun.com/) to upgrade your instance.
-   We recommend that you execute only MaxCompute DDL statements in Hologres. If you need to execute MaxCompute DML statements, execute them in MaxCompute.
-   You can execute only one SQL statement at a time.

## Syntax

-   **Sample code**

    ```
    select exec_external_sql(
      'server', 
      'database', 
      'sql' , 
      timeout_ms, 
      'options'
    );
    ```

    Specify the preceding parameters in sequence. If specific parameters need to be omitted, use the parameter names as the parameter values. Example:

    ```
    exec_external_sql(
                server:='odps_server' , 
                database:='odps_project_name' , 
                sql:='sql' , 
                timeout_ms:=timeout_ms , 
                options:='options'
    )
    ```

-   **Parameters**

    |Parameter|Description|Remarks|Example|
    |---------|-----------|-------|-------|
    |server|The server where foreign tables reside. You can use the odps\_server server that is created at the underlying layer of Hologres. The exec\_external\_sql function supports only the odps\_server server. For more information about foreign servers, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k).|If you do not specify a server, the odps\_server server is used by default.|`'odps_server'`|
    |database|The name of the MaxCompute project.|N/A|`'seahawks'`|
    |sql|The MaxCompute SQL statement that you want to execute. We recommend that you execute only MaxCompute DDL statements. Valid values:    -   create table
    -   alter table
    -   desc table
    -   drop table
|The SQL statement must conform to the MaxCompute SQL syntax. We recommend that you execute only MaxCompute DDL statements. If the SQL statement contains single quotation marks \(' '\), you must add double dollar signs \($$\) before and after the SQL statement to escape the single quotation marks. Example: `$$SQL$$`. **Note:** Escape characters are not supported on the ad hoc query pages of HoloWeb and HoloStudio.

|`'CREATE TABLE IF EXISTS MC_TBL ;'`|
    |timeout\_ms|The timeout period for the execution of the SQL statement. Unit: milliseconds.|If you do not specify a timeout period or specify a value smaller than 0, this parameter is set to 60000 by default. If the timeout period expires, Hologres stops executing the SQL statement and sends an instruction to MaxCompute to cancel the execution of the SQL statement.|50000|
    |options|The SQL properties that you can set when you submit SQL statements in the DataWorks console or on the MaxCompute client.|For more information, see [SET operations](/intl.en-US/Development/Common commands/SET operations.md). If you need to set multiple properties, change the data type of this parameter to JSON.|`{"odps.sql.type.system.odps2":"true"}` or `{"odps.sql.type.system.odps2":"true", "odps.sql.decimal.odps2=true":"true"}`|


## Examples

You can execute MaxCompute SQL statements in Hologres. If you execute the CREATE TABLE statement, a table is created in the MaxCompute project that you specify. We recommend that you execute only MaxCompute DDL statements in Hologres.

-   **Example 1: Create a non-partitioned MaxCompute table**

    ```
    select exec_external_sql(
                'odps_server',
                'mc_project' , -- Specify the name of the MaxCompute project.
                'create table par_mc_table(id int,name string);' , -- Create a MaxCompute table.
                5000 -- Set the timeout period to 5,000 ms.
                );                
    ```

-   **Example 2: Create a partitioned MaxCompute table and specify a partition field**

    ```
    -- Create a partitioned table.
    select exec_external_sql(
              server:='odps_server',
              database:='mc_project', -- Specify the name of the MaxCompute project.
              sql:='create table par_mc_table(id int,name string) partitioned by (pt string);', -- Create a partitioned table.
              timeout_ms:=10000 -- Set the timeout period to 10,000 ms.
                      );
    -- Specify a partition field for the partitioned table.
    select exec_external_sql(
               'odps_server',
               'mc_project', -- Specify the name of the MaxCompute project.
               $$ALTER TABLE par_mc_table ADD IF NOT EXISTS partition(pt='202102');$$ -- Specify a partition field.
                      );
    ```

-   **Example 3: Create a MaxCompute table across regions**

    ```
    select exec_external_sql(
                'hangzhou_odps_server' ,
                'hologres_test' , 
                'create table mc_test(id int,create_time datetime,decimal_column decimal(38, 10));' ,
                50000, -- Set the timeout period to 50,000 ms.
                '{                 
                "odps.sql.type.system.odps2":"true",
                "odps.sql.decimal.odps2=true":"true"
      }' -- Set MaxCompute SQL properties. In this example, new data types and the DECIMAL type are enabled.          
    ```

-   **Example 4: Delete a MaxCompute table**

    ```
    select exec_external_sql(
                'odps_server',
                'mc_project', -- Specify the name of the MaxCompute project.
                'drop table if exists mc_table;' , -- Delete a MaxCompute table.
                50000
                );               
    ```

-   **Example 5: Export data to MaxCompute by executing SQL statements**

    In Hologres V0.9 and later, Hologres allows you to [export data to MaxCompute by executing SQL statements](/intl.en-US/Data Access/Batch synchronization/MaxCompute/Export data to MaxCompute by executing SQL statements.md). Before you export data, you must create a table in MaxCompute to receive data. Previously, this is inconvenient. In Hologres V0.10 and later, you can execute MaxCompute SQL statements in Hologres. This way, you can create a MaxCompute table and export data in Hologres.

    This example shows you how to export data from a non-partitioned Hologres table to a non-partition MaxCompute table. You can perform the following steps:

    1.  Create a Hologres internal table whose data is to be exported to MaxCompute. For example, use the following DDL statements to create a table named holo\_table and insert the sample data into the table:

        ```
        creat table "public"."holo_table" (
         "id" int4,
         "name" text
        );
        
        insert into "public"."holo_table" values 
        (1,'a'),
        (2,'b'),
        (3,'c');
        ```

    2.  Create a MaxCompute table to receive data.

        ```
        select exec_external_sql(
                    'odps_server',
                    'mc_project' , -- Specify the name of the MaxCompute project.
                    'create table mc_sink_table(id int,name string);' , -- Create a MaxCompute table to receive data.
                    5000 -- Set the timeout period to 5,000 ms.
                    );
        ```

    3.  Create a foreign table in Hologres to map the source table in MaxCompute.

        ```
        begin;
        create foreign table "public"."mc_mapping_foreign_table" (
         "id" int4,
         "name" text
        )
        server odps_server_bj
        options (project_name 'default_project_2361b62', table_name 'mc_sink_table');
        commit;
        ```

    4.  Execute the following SQL statements as required in Hologres to export data to MaxCompute:

        -   Export the data of all fields.

            ```
            set hg_experimental_enable_write_maxcompute = on; -- You must use a Grand Unified Configuration (GUC) parameter because this data export feature is in a beta version.
            insert into mc_mapping_foreign_table
            select * from holo_table;
            ```

        -   Export the data of specific fields.

            ```
            set hg_experimental_enable_write_maxcompute = on; -- You must use a GUC parameter because this data export feature is in a beta version.
            insert into mc_mapping_foreign_table (name)
            select name from holo_table;
            ```

        For more information, see [Export data to MaxCompute by executing SQL statements](/intl.en-US/Data Access/Batch synchronization/MaxCompute/Export data to MaxCompute by executing SQL statements.md).


