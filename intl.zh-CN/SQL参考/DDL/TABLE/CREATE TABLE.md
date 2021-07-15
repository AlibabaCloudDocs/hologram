---
keyword: [创建表, Hologres, CREATE TABLE]
---

# CREATE TABLE

CREATE TABLE语句用于创建表。本文为您介绍在交互式分析Hologres中CREATE TABLE的用法。

## 建表

1.  **命令格式**

    目前Hologres 语法是PostgreSQL的一个子集，支持的建表语法如下。

    ```
    begin;
    create table [if not exists] [schema_name.]table_name ([
      {
       column_name column_type [column_constraints, [...]]
       | table_constraints
       [, ...]
      }
    ]);
    call set_table_property('<table_name>', property, value);
    commit;
    ```

2.  **参数说明**
    1.  column\_type：为字段的数据类型，已支持的数据类型可以参见[数据类型汇总](/intl.zh-CN/SQL参考/数据类型/数据类型汇总.md)。
    2.  列约束`column_constraints`和表约束`table_constraints`的支持情况如下。

        |参数|column\_constraints|table\_constraints|
        |--|-------------------|------------------|
        |primary key|支持|支持|
        |not null|支持|-|
        |null|支持|-|
        |unique|不支持|不支持|
        |check|不支持|不支持|
        |default|支持|不支持|

    3.  set\_table\_property为表设置属性，详请参见[设置表属性](#section_l9q_k83_z01)。
3.  **使用限制**
    1.  支持将多个字段设置为primary key（即复合主键）。被设置为primary key的字段是唯一且非空的列或者列组合，同时只能在一个语句里设置多列为表的primary key。目前primary key不支持Float、Double、Numeric、Array、Json、Date及其他复杂数据类型。如下示例指导您将id和ds设置为表的primary key。

        ```
        BEGIN;
        CREATE TABLE public.test (
         "id" text NOT NULL,
         "ds" text NOT NULL,
        PRIMARY KEY (id,ds)
        );
        CALL SET_TABLE_PROPERTY('public.test', 'orientation', 'column');
        COMMIT;
        ```

    2.  表名和列名均对大小写不敏感，如需定义大写表名、大写列名、特殊字符表名或列名，可使用双引号（““）进行转义。示例如下：

        ```
        create table "TBL"(a int);
        select relname from pg_class where relname = 'TBL';
        insert into "TBL" values (-1977);
        select * from "TBL";
        ------------------------------------------------------------------
        begin;
        create table tbl ("C1" int not null);
        call set_table_property('tbl', 'clustering_key', '"C1"');
        commit;
        ------------------------------------------------------------------
        begin;
        create table tbl ("C1" int not null, c2 text);
        call set_table_property('tbl', 'clustering_key', '"C1,c2:desc"');  -- set_table_property 见下文
        call set_table_property('tbl', 'segment_key', '"C1",c2:desc');
        commit;
        ------------------------------------------------------------------
        create table "Tab_$A%*" (a int);
        select relname from pg_class where relname = 'Tab_$A%*';
        insert into "Tab_$A%*" values (-1977);
        select * from "Tab_$A%*";
        ```

    3.  `IF NOT EXISTS`：在创建表时，如果不存在同名表且语义正确，表创建都会返回成功。如果不指定`IF NOT EXISTS`选项而存在同名表，则返回异常。如果指定`IF NOT EXISTS`选项，Hologres会提示信息，跳过表创建步骤，返回成功，直观的规则如下。

        |配置项|指定if not exists|不指定if not exists|
        |---|---------------|----------------|
        |存在同名表|`NOTICE：relation “xx“already exists，skippingSUCCEED`|`ERROR：relation is already exists.`|
        |不存在同名表|`SUCCEED`|`SUCCEED`|

    4.  表名的长度不超过64字节，超过64字节将被截断。
4.  **使用示例**

    示例建一张含主键的普通表。

    ```
    CREATE TABLE test (
     "id" bigint NOT NULL,
     "name" text NOT NULL,
     "age" bigint,
     "class" text NOT NULL,
    PRIMARY KEY (id)
    );
    ```


## 设置表属性

在Hologres中，可以通过set\_table\_property为表设置多种属性，合理的表属性设置可以有助于系统高效地组织和查询数据。与数据存储布局有关的参数需要和建表语句同时执行，其中orientation、distribution\_key、clustering\_key和event\_time\_column创建后当前版本不支持修改。

1.  **命令格式**

    ```
    call set_table_property('<table_name>', property, value)
    ```

    **说明：** ：`set_table_property`的调用需要与`create table`在同一事务中执行。

    Hologres当前版本支持的设置表属性有以下几种。

    ```
    call set_table_property('table_name', 'orientation', '[column | row]'); 
    call set_table_property('table_name', 'distribution_key', '[columnName[,...]]');
    call set_table_property('table_name', 'clustering_key', '[columnName{:[desc|asc]} [,...]]'); 
    call set_table_property('table_name', 'event_time_column', '[columnName [,...]]');
    call set_table_property('table_name', 'bitmap_columns', '[columnName{:[on|off]}[,...]]');
    call set_table_property('table_name', 'dictionary_encoding_columns', '[columnName{:[on|off|auto]}[,...]]');
    call set_table_property('table_name', 'time_to_live_in_seconds', '<non_negative_literal>');
    ```

2.  **参数说明**

    具体参数和相关内容如下表所示：

    |参数|列存表|行存表|
    |--|---|---|
    |orientation|column（默认值）|row|
    |distribution\_key|默认为主键，根据业务场景修改。|默认为主键。|
    |clustering\_key|默认为空。|默认为主键。|
    |event\_time\_column|默认为第一个非空时间戳字段。|不支持。|
    |bitmap\_columns|按需使用。|不支持。|
    |dictionary\_encoding\_columns|按需使用。|不支持。|
    |time\_to\_live\_in\_seconds|按需使用。|按需使用。|

    1.  **orientation**

        ```
        call set_table_property('<table_name>', 'orientation', '[column | row]');
        ```

        -   orientation：指定了数据库表在Hologres中的存储模式是列存还是行存，当前版本仅支持一种数据存储方式。
        -   在Hologres中表默认为列存（column store）形式。**列存对于OLAP场景较为友好，适合各种复杂查询、数据关联、扫描、过滤、统计。行存对于key-value场景比较友好，适合基于primary key的点查和扫描scan**。列存会默认创建更多的索引，包括对字符串类型创建bitmap索引，这些索引可以显著加速查询过滤和统计，因此列比较多的表，会占用更多的存储空间，您可以通过关闭这些默认创建的索引，释放空间。行存默认仅对主键创建索引，仅支持主键的快速查询，因此使用的存储空间更少，但使用场景也受到限制。
        -   使用示例

            ```
            //创建行存表
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'orientation', 'row');
            commit;
            
            //创建列存表
            begin;
            create table tb2 (a int not null, b text not null);
            call set_table_property('tb2', 'orientation', 'column');
            commit;
            ```

    2.  **distribution\_key**

        ```
        call set_table_property('<table_name>', 'distribution_key', '[columnName[,...]]');
        ```

        -   distribution\_key：指定了数据库表分布策略。数据根据distribution\_key被分配到各个shard上。系统保证distribution\_key相同的记录会被分配到同一个shard上。
        -   columnName部分如设置单列，不要有多余空格。如设置多列，则以逗号分隔，同样不要有多余的空格。
        -   distribution\_key指定的列或列组合不支持Float、Double、Numeric、Date、Timestamp、Timestamptz、Array、Json、Serial、Bytea及其他复杂数据类型。
        -   当表中有primary key时，distribution\_key默认为primary key。distribution\_key必须为primary key或者primary key中的部分字段（不能为空），同一记录的数据只能属于一个shard。当表中没有primary key时，对distribution\_key没有限制，可以为空（不指定任何列）。如果distribution\_key为空，即随机shuffle，数据随机分布到不同shard上。当distribution\_key对应列的值为空时，当作“”（空串）看待。
        -   Hologres中，distribution\_key是非常重要的分布式概念。合理的设置distribution\_key可以达到如下效果：
            -   提高性能。不同的Shard可以进行并行计算，从而提高性能。
            -   提高QPS。当您以distribution\_key做过滤条件时，Hologres可以直接筛选出数据相关的Shard进行扫描。否则，Hologres需要让所有的Shard参与计算，会影响QPS。
            -   提高Join性能。当两张表在同一个Table Group内，并且Join key是distribution\_key时，那么数据分布已保证表A一个Shard内的数据和表B同一Shard内的数据对应，只需要直接在本节点Join本节点数据（Local Join）即可，可以大大提高执行效率。
        -   使用示例

            ```
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'distribution_key', 'a');
            commit;
            
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'distribution_key', 'a,b');
            commit;
            
            begin;
            create table tbl1(a int not null, b text not null);
            call set_table_property('tbl1', 'distribution_key', 'a');
            create table tbl2(c int not null, d text not null);
            call set_table_property('tbl2', 'distribution_key', 'c');
            commit;
            
            select b, count(*) from tbl1 join tbl2 on tbl1.a = tbl2.c group by b;
            ```

    3.  **clustering\_key**

        ```
        call set_table_property('tbl', 'clustering_key', '[columnName{:asc} [,...]]');
        ```

        -   clustering\_key：在指定的列上建立聚簇索引。Hologres会在聚簇索引上对数据进行排序，建立聚簇索引能够加速在索引列上的range和filter查询。
        -   必须为`not nullable`的列或者列组合，不支持Float、Double、Array、Json及其他复杂数据类型。
        -   clustering\_key指定列时，可在列名后添加 `:asc`（升序）来表明构建索引时的排序方式。
        -   行存表的clustering\_key默认为主键 （V0.9之前的版本默认不设置）。如果设置为和主键不同的clustering\_key，那么Hologres会为这张表生成两个排序（`primary_key`排序和`clustering_key`排序），造成数据冗余。
        -   列存表的clustering\_key默认为空。
        -   由于clustering\_key用于排序，所以clustering\_key里的列组合排在前面的优先级更高，clustering\_key建议仅保留1～2列。
        -   clustering\_key可以用于在clustering index最开始几列的range和filter的加速查询，即**查询具备左匹配原则，不匹配则无法利用clustering\_key查询加速**。

            假设表table1的clustering\_key设置为col1和col2，那么下面的query可以被加速：

            ```
            --可加速
            select * from table1 where col1='abc'; 
            --可加速
            select * from table1 where col1>'xxx' and col1<'abc';
            --可加速
            select * from table1 where col1 in ('abc','def');
            --可加速
            select * from table1 where col1='abc' and col='def'; 
            --不可加速
            select col1,col4 from table1 where col2='def';
            ```

        -   使用示例

            ```
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'clustering_key', 'a,b');
            commit;
            -------------------------------------------------------------
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'clustering_key', 'a,b:asc');
            commit;
            ```

    4.  **event\_time\_column**

        ```
        call set_table_property('table_name', 'event_time_column', '[columnName [,...]]');
        ```

        -   event\_time\_column：原名为segment\_key，在 V0.9 版本默认改名为event\_time\_column，segment\_key依旧向下兼容使用。其用途为指定时间列作为分段键，必须为时间类型强相关的列 （如果数据有更新的话，需要和update time强相关）。当查询条件包含event\_time\_column时，查询可以通过event\_time\_column快速找到相应数据对应的存储位置。适用于日志、流量等和时间强相关的数据，合理设置可极大提升性能。
        -   设置event\_time\_column要求orientation为column，即列存表。
        -   event\_time\_column指定的列必须满足非空约束（not null），不支持Float、Double、Array、Json及其他复杂数据类型。
        -   列存表默认将table schema中的第一个非空的timestamp/timestamptz的字段作为event\_time\_column，如果不存在这样的字段，则默认将第一个非空的date类型的字段作为event\_time\_column （V0.9之前的版本默认为空）。
        使用示例

        ```
        begin;
        create table tbl (a int not null, b text not null);
        call set_table_property('tbl', 'event_time_column', 'a,b');
        commit;
        -------------------------------------------------------------
        begin;
        create table tbl (a int not null, b time not null);
        call set_table_property('tbl', 'event_time_column', 'b:asc');
        commit;
        ```

    5.  **bitmap\_columns**

        ```
        call set_table_property('<table_name>', 'bitmap_columns', '[columnName{:[on|off]}[,...]]');
        ```

        其中，参数说明如下表所示：

        |参数|说明|
        |--|--|
        |table\_name|表名称。需要与待修改的表名大小写保持一致，可以携带Schema信息。|
        |on|当前字段打开bitmap\_columns。|
        |off|当前字段关闭bitmap\_columns。|

        -   bitmap\_columns：比特编码列。bitmap可以对存储文件内部的数据进行快速过滤，所以建议把filter条件的数据建成比特编码。
        -   设置bitmap\_columns要求表的存储形式为column，即列存表。
        -   bitmap\_columns适合取值不多的列，对于每个取值构造一个二进制串，表示取值所在位置的bitmap。
        -   bitmap\_columns指定的列可以为空。
        -   当前版本默认所有text列都会被隐式地设置到bitmap\_columns中（Hologres V0.8和Hologres V0.9版本行为一致）。
        -   **可以再事务之外单独使用，表示修改bitmap\_columns列，修改之后非立即生效，比特编码构建和删除在后台异步执行。**详请参见[ALTER TABLE](/intl.zh-CN/SQL参考/DDL/TABLE/ALTER TABLE.md)。
        -   使用示例

            ```
            --创建tbl并设置bitmap索引
            begin;
            create table tbl (
              a int not null, 
              b text not null);
            call set_table_property('tbl', 'bitmap_columns', 'a:on,b:off');
            commit;
            
            --全量修改bitmap索引（除了call里面设定的字段，其余text字段会自动设置为bitmap索引）
            call set_table_property('tbl', 'bitmap_columns', 'a:off');
            --增量修改bitmap索引（只修改指定的字段是否为bitmap）
            call update_table_property('tbl', 'bitmap_columns', 'b:off');
            ```

    6.  **dictionary\_encoding\_columns**

        ```
        call set_table_property('<table_name>', 'dictionary_encoding_columns', '[columnName{:[on|off|auto]}[,...]]');
        ```

        其中，参数说明如下表所示：

        |参数|说明|
        |--|--|
        |table\_name|表名称。需要与待修改的表名大小写保持一致，可以携带Schema信息。|
        |on|表示当前字段打开dictionary\_encoding\_columns。|
        |off|表示当前字段关闭dictionary\_encoding\_columns。|
        |auto|表示自动。如果设置了auto，Hologres会根据所在列数值的重复程度自动选择是否进行dictionary\_encoding\_columns，值的重复度越高，字典编码的收益越大。在Hologres V0.8版本及更早版本中默认所有text列都会被设置为dictionary\_encoding\_columns，在Hologres V0.9版本及之后版本，会根据数据特征自动选择是否创建字典编码。|

        -   dictionary\_encoding\_columns：字典编码列，为指定列的值构建字典映射。字典编码可以将字符串的比较转成数字的比较，加速group by、filter等查询。
        -   设置dictionary\_encoding\_columns要求表的存储形式为column，即列存表。
        -   dictionary\_encoding\_columns指定的列可以为null。
        -   取值较少的列适合设置dictionary\_encoding\_columns，可以压缩存储。
        -   V0.8及更早版本中默认所有text列都会被隐式地设置到dictionary\_encoding\_columns中。V0.9及之后的版本会根据数据特征自动选择是否创建字典编码。
        -   **可以在事务之外单独使用。表示修改dictionary\_encoding\_columns列，修改之后非立即生效，字典编码构建和删除在后台异步执行。**详请参见[ALTER TABLE](/intl.zh-CN/SQL参考/DDL/TABLE/ALTER TABLE.md)。
        -   使用示例

            ```
            --创建表tbl并设置dictionary_encoding_columns索引
            begin;
            create table tbl (
              a int not null, 
              b text not null,
              c text not null
            );
            call set_table_property('tbl', 'dictionary_encoding_columns', 'a:on,b:off,c:auto');
            commit;
            
            --全量修改dictionary_encoding_columns索引（除了call里面设定的字段，其余text字段会自动设置为dictionary_encoding_columns索引）
            call set_table_property('tbl', 'dictionary_encoding_columns', 'a:off');
            --增量修改dictionary_encoding_columns索引（只修改指定的字段是否为dictionary_encoding_columns）
            call update_table_property('tbl', 'dictionary_encoding_columns', 'b:off');
            ```

    7.  **time\_to\_live\_in\_seconds**

        ```
        call set_table_property('<table_name>', 'time_to_live_in_seconds', '<non_negative_literal>');
        ```

        -   time\_to\_live\_in\_seconds：表数据的生存时间，单位为秒，必须是非负数字类型，整数或浮点数均可。
        -   可以在事务之外单独使用，表示修改表数据生存时间。
        -   使用示例

            ```
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'time_to_live_in_seconds', '3.14159');
            commit;
            
            --修改TTL
            call set_table_property('tbl', 'time_to_live_in_seconds', '86400');
            ```

            **说明：** 表数据的TTL并不是精确的时间，当超过设置的TTL后，系统会在某一个时间自动删除表数据，所以业务逻辑不能强依赖TTL。若是想精确的删除表数据，可以使用HoloStudio数据开发，进行调度任务配置来删除数据。


## 增加注释

Hologres支持给表、外表、列等增加注释（comment）的功能。

增加注释的使用示例如下：

```
-- 给表增加注释
COMMENT ON TABLE table_name IS 'my comments on table table_name.';

-- 给列增加注释
COMMENT ON COLUMN table_name.col1 IS 'This my first col1';

-- 给外部表增加注释
COMMENT ON FOREIGN TABLE foreign_table IS ' comments on my foreign table';
```

更多关于注释的用法请参见[PostgreSQL comment](https://www.postgresql.org/docs/11/sql-comment.html)。

## 查看表结构

您可以执行如下命令查看TABLE的具体DDL：

```
--该命令是DB级别，一个DB执行一次即可
create extension hg_toolkit;
--您可以将tablename替换为实际的表名称
select hg_dump_script('tablename');
```

