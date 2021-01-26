---
keyword: [修改表, Hologres, DDL, ALTER TABLE]
---

# ALTER TABLE

ALTER TABLE语句用于修改表。本文为您介绍ALTER TABLE的用法。

## 注意事项

Hologres当前对表的修改有如下注意事项：

-   支持对表进行重命名和增加列的操作。
-   对于外部表\(foreign table\)没有操作限制。
-   针对Hologres的分区表，还支持ATTACH PARTITION和DETACH PARTITION两种修改。

## 重命名

ALTER TABLE语句可以对表进行重命名，如果目标表不存在，或者重命名目标表为已存在的表名称，系统均会返回异常。

-   使用语法

    ```
    //将table_name重命名为new_table_name，具体表名称可以根据需求填写
    ALTER TABLE table_name RENAME TO new_table_name;
    
    //将外部表my_foreign_table_name重命名为my_new_foreign_table_name，具体表名称可以根据需求填写
    ALTER FOREIGN TABLE my_foreign_table_name TO my_new_foreign_table_name;
    ```

-   使用示例

    ```
    ALTER TABLE test RENAME TO holo_test ;//将表test重命名为holo_test
    ALTER FOREIGN TABLE odps_test TO﻿ my_odps_test;//将外部表odps_test重命名为my_odps_test
    ```


## 增加列

ALTER TABLE语句可以给表增加列。

-   使用语法

    ```
    //在表table_name中增加new_column_name data_type列，具体表名称和列名称可以根据需求填写
    ALTER TABLE IF EXISTS table_name ADD COLUMN new_column_name data_type;
    
    //在表table_name中增加列，具体表名称和列名称可以根据需求填写
    ALTER TABLE IF EXISTS table_name ADD COLUMN col_add_1 data_type, ADD COLUMN col_add_2 TEXT IF NOT EXISTS col_add_2 data_type; 
    ```

-   使用示例

    ```
    ALTER TABLE IF EXISTS holo_test ADD COLUMN id int;//在表holo_test中增加id列
    ```


## 修改表属性

Hologres支持通过执行语句修改参数，达到修改表属性的目的。具体修改方式说明如下：

-   修改**dictionary\_encoding\_columns**字典编码列
    -   使用语法

        ```
        CALL UPDATE_TABLE_PROPERTY('table_name', 'dictionary_encoding_columns', '[columnName{:[on|off|auto]}[,...]]');
        ```

    -   参数说明

        |参数|说明|
        |--|--|
        |table\_name|需要和待修改的表名大小写保持一致，可以携带Schema信息。|
        |on|表示当前字段打开**dictionary\_encoding\_columns**。|
        |off|表示当前字段关闭**dictionary\_encoding\_columns**。|
        |auto|表示自动。如果是设置了auto，Hologres会根据所在列数值的重复程度自动选择是否进行**dictionary\_encoding\_columns**，值的重复度越高，字典编码的收益越大。在Hologres V0.8版本及更早版本中默认所有text列都会被设置为**dictionary\_encoding\_columns**，在Hologres V0.9版本及之后版本，会根据数据特征自动选择是否创建字典编码。|

    -   使用示例

        ```
        CREATE TABLE test (
         a text NOT NULL,
         b text NOT NULL,
         c text NOT NULL,
         d text
        );
        CALL UPDATE_TABLE_PROPERTY('test','dictionary_encoding_columns','a:on,b:auto');
        ```

-   修改**bitmap\_columns**比特编码列
    -   使用语法

        ```
        CALL UPDATE_TABLE_PROPERTY('table_name', 'bitmap_columns', '[columnName{:[on|off]}[,...]]');
        ```

    -   参数说明

        |参数|说明|
        |--|--|
        |table\_name|需要和待修改的表名大小写保持一致，可以携带Schema信息。|
        |on|当前字段打开**bitmap\_columns**。|
        |off|当前字段关闭**bitmap\_columns**。|

    -   使用示例

        ```
        CREATE TABLE test (
         a text NOT NULL,
         b text NOT NULL,
         c text NOT NULL,
         d text
        );
        CALL UPDATE_TABLE_PROPERTY('test','bitmap_columns','a:on,b:off');
        ```

-   修改表数据的生存时间
    -   使用语法

        ```
        CALL SET_TABLE_PROPERTY('tbl', 'time_to_live_in_seconds', '<non_negative_literal>');
        ```

    -   参数说明

        |参数|说明|
        |--|--|
        |time\_to\_live\_in\_seconds|简称TTL，表数据的生存时间，单位为秒，必须是非负数字类型，整数或浮点数均可。|

        **说明：** 表数据生存时间是按照数据写入Hologres开始，在指定的时间内未被执行更新等操作，超过该指定时间，表将会在某个时间内被删除，但并不是精准的时间。

    -   使用示例

        ```
        CALL SET_TABLE_PROPERTY('tbl', 'time_to_live_in_seconds', '86400');
        ```


