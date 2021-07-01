# 执行MaxCompute SQL语句

从Hologres V0.10版本开始，Hologres与MaxCompute集成性进一步提升，支持在Hologres中执行MaxCompute的SQL语句，方便您直接快速操作MaxCompute。本文将为您介绍，在Hologres中如何执行MaxCompute SQL语句。

## 前提条件

-   已开通Hologres并连接开发工具，本文使用HoloWeb，详请参见[HoloWeb快速入门](/cn.zh-CN/快速入门/HoloWeb快速入门.md)。
-   已开通MaxCompute，详请参见[通过查询编辑器使用MaxCompute](/cn.zh-CN/快速入门/通过查询编辑器使用MaxCompute.md)。
-   在Hologres中执行MaxCompute SQL需要当前用户具有在MaxCompute中执行SQL的权限。关于MaxCompute的权限，详请参见[用户与权限管理](/cn.zh-CN/安全管理/安全管理基础/用户与权限管理.md)。

## 使用限制

-   仅Hologres V0.10 及以上版本支持在Hologres中执行MaxCompute SQL语句。请前往Hologres管控台的实例详情页查看当前实例版本，如果您的实例是V0.10以下版本，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)升级实例。
-   目前建议您在Hologres中仅执行MaxCompute的DDL语句，如果需要执行DML语句，请前往MaxCompute进行操作。
-   每次调用只能执行一条SQL语句。

## 命令语法

-   **语法示例**

    ```
    select exec_external_sql(
      'server', 
      'database', 
      'sql' , 
      timeout_ms, 
      'options'
    );
    ```

    如上参数必须要按照顺序填写，若是需要省略某些参数，需要显示指定参数名，如下所示：

    ```
    exec_external_sql(
                server:='odps_server' , 
                database:='odps_project_name' , 
                sql:='sql' , 
                timeout_ms:=timeout_ms , 
                options:='options'
    )
    ```

-   **参数说明**

    |参数名|参数含义|说明|示例|
    |---|----|--|--|
    |server|外部服务器名称。您可以直接调用Hologres底层已创建的名为odps\_server的外部表服务器。此函数目前仅支持odps server，外部server详细原理请参见[Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k)。|如果为空字符串，会取odps\_server作为外部server。|`'odps_server'`|
    |database|MaxCompute的project名。|无|`'seahawks'`|
    |sql|需要执行的MaxCompute SQL（建议只执行DDL语句）：    -   create table
    -   alter table
    -   desc table
    -   drop table
|SQL语句需要符合MaxCompute的语法，建议只执行DDL语句。如果SQL中有单引号，需要在SQL语句前后添加双美元符号（`$$SQL$$`）实现单引号转义。**说明：** 在HoloWeb和HoloStudio的临时查询页面暂不支持转义字符。

|`'CREATE TABLE IF EXISTS MC_TBL ;'`|
    |timeout\_ms|执行超时时间，单位ms。|缺省或者小于0的情况下会将该参数值设置为60000ms。超过超时时间将会退出，同时向MaxCompute发送一个取消指令。|50000|
    |options|使用DataWorks或者MaxCompute客户端提交SQL时，通常需要设置的SQL Flag。|具体的配置项请参见[SET操作](/cn.zh-CN/开发/常用命令/SET操作.md)，若是有多个flag需要设置，需要将字段类型改成Json格式。|`{"odps.sql.type.system.odps2":"true"}`或者`{ "odps.sql.type.system.odps2":"true", "odps.sql.decimal.odps2=true":"true"}`|


## 使用示例

您可以在Hologres中执行MaxCompute SQL，如果执行建表语句则会在MaxCompute对应的project中创建一张表。当前版本建议仅执行DDL语句。

-   **示例一：创建MaxCompute非分区表**

    ```
    select exec_external_sql(
                'odps_server',
                'mc_project' , --project名
                'create table par_mc_table（id int,name string);' ,--在MaxCompute中创建一张表
                5000  --超时时间为5000ms
                );                
    ```

-   **示例二：创建MaxCompute分区表并指定分区**

    ```
    --创建分区表
    select exec_external_sql(
              server:='odps_server',
              database:='mc_project', --maxcompute的project名
              sql:='create table par_mc_table（id int,name string) partitioned by (pt string);', --创建分区表
              timeout_ms:=10000--超时时间为10000ms
                      );
    --指定分区表的分区
    select exec_external_sql(
               'odps_server',
               'mc_project', --maxcompute的project名
               $$ALTER TABLE par_mc_table ADD IF NOT EXISTS partition(pt='202102');$$--指定分区
                      );
    ```

-   **示例三：跨region创建MaxCompute表**

    ```
    select exec_external_sql(
                'hangzhou_odps_server' ,
                'hologres_test' , 
                'create table mc_test(id int,create_time datetime,decimal_column decimal(38, 10));' ,
                50000,--超时时间
                '{                 
                "odps.sql.type.system.odps2":"true",
                "odps.sql.decimal.odps2=true":"true"
      }'--MaxCompute的flag设置，分别代表开启新数据类型和开启decimal类型          
    ```

-   **示例四：删除MaxCompute中的表**

    ```
    select exec_external_sql(
                'odps_server',
                'mc_project',  --MaxCompute的project名
                'drop table if exists mc_table;' ,--删除MaxCompute的表
                50000
                );               
    ```

-   **示例五：配合“通过SQL方式导出MaxCompute”功能使用**

    从Hologres V0.9版本开始，Hologres支持[通过SQL导出数据至MaxCompute](/cn.zh-CN/数据接入/离线同步/MaxCompute/通过SQL导出数据至MaxCompute.md)，但是该方法需要在MaxCompute中提前创建接收数据的表，操作比较麻烦。从Hologres V0.10版本开始，通过在Hologres中执行MaxCompute SQL，即可创建表，再将数据导出，支持一站式开发。

    示例操作将Hologres中非分区数据导入至MaxCompute非分区表。具体如下：

    1.  在Hologres准备一张Hologres内部表（例如：holo\_table），用于导出数据至MaxCompute，示例DDL和数据如下：

        ```
        create table "public"."holo_table" (
         "id" int4,
         "name" text
        );
        
        insert into "public"."holo_table" values 
        (1,'a'),
        (2,'b'),
        (3,'c');
        ```

    2.  创建一张MaxCompute表，用于在MaxCompute中接收数据。

        ```
        select exec_external_sql(
                    'odps_server',
                    'mc_project' , --project名
                    'create table mc_sink_table（id int,name string);' ,--在MaxCompute中创建一张接收数据表
                    5000--超时时间为5000ms
                    );
        ```

    3.  在Hologres新建一张外部表，用于映射MaxCompute表。

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

    4.  在Hologres通过SQL语句导出数据至MaxCompute。

        -   全部字段数据导出

            ```
            set hg_experimental_enable_write_maxcompute = on;-- 由于是beta功能，需要打开GUC参数
            insert into mc_mapping_foreign_table
            select * from holo_table;
            ```

        -   部分字段数据导出

            ```
            set hg_experimental_enable_write_maxcompute = on;-- 由于是beta功能，需要打开GUC参数
            insert into mc_mapping_foreign_table (name)
            select name from holo_table;
            ```

        更多关于数据导出的操作说明请参见[通过SQL导出数据至MaxCompute](/cn.zh-CN/数据接入/离线同步/MaxCompute/通过SQL导出数据至MaxCompute.md)。


