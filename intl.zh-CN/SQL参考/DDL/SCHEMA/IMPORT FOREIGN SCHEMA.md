---
keyword: [批量创建外部表, Hologres]
---

# IMPORT FOREIGN SCHEMA

IMPORT FOREIGN SCHEMA语句用于批量创建外部表。本文为您介绍IMPORT FOREIGN SCHEMA语句的用法和使用限制。

## 使用限制

使用`IMPORT FOREIGN SCHEMA`语句时，建议您添加`LIMIT TO`限制，并使用括号将需要添加限制的表名称括起来。如果不添加该限制，系统则将目标MaxCompute工作空间中的所有表批量创建至Hologres中。

## 命令格式

在Hologres中批量创建外部表的命令格式如下。

```
IMPORT FOREIGN SCHEMA remote_schema
    [ { LIMIT TO | EXCEPT } ( table_name [, ...] ) ]
    FROM SERVER odps_server
    INTO local_schema 
    [ OPTIONS ( option 'value' [, ... ] ) ]
```

## 参数说明

参数说明如下表所示。

|参数|描述|
|--|--|
|remote\_schema|需要导入的MaxCompute表所在的项目名称。|
|table\_name|需要导入的MaxCompute表名称。|
|server\_name|MaxCompute表所在的外部服务器名称，默认为**odps\_server**。您可以直接调用Hologres底层已创建的名为**odps\_server**的外部表服务器。详细原理请参见[Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k)。 |
|local\_schema|Hologres外部表所在的schema名（如public）。|
|options|Hologres支持如下两个option：-   if\_table\_exist：表示导入时已经存在该表。取值如下：
    -   error：默认值，表示已有同名外部表，不再重复创建。
    -   ignore：忽略该同名表，跳过该表的导入，使导入的表不重复。
    -   update：更新并重新导入该表。
-   if\_unsupported\_type：表示导入的外部表中存在Hologres不支持的数据类型。取值如下：
    -   error：报错，导入失败， 并提示哪些表存在不支持的类型。
    -   skip：默认值，表示跳过导入的存在不支持类型的表，并提示哪些表被跳过。 |

**说明：** Hologres仅支持创建MaxCompute外部表。新建的外部表名称需要同MaxCompute表的名称一致。

## 使用示例

示例选取MaxCompute公共数据集**public\_data**中的表，在Hologres中批量创建外部表。您可以参照[t12301.md\#section\_9hk\_a70\_qsg](/intl.zh-CN/公开数据集/概述.md)描述，登录并查询数据集 。

-   示例1：为public schema新建一张外部表，若表存在则更新表。

    ```
    IMPORT FOREIGN SCHEMA public_data LIMIT to
    (customer) 
      FROM server odps_server INTO PUBLIC options(if_table_exist 'update');
    ```

-   示例2：为public schema批量新建外部表。

    ```
     IMPORT FOREIGN SCHEMA public_data LIMIT to(
      customer,
      customer_address,
      customer_demographics,
      inventory,item,
      date_dim,
      warehouse) 
      FROM server odps_server INTO PUBLIC options(if_table_exist 'update');
    ```

-   示例3：新建一个holotest schema并批量新建外部表。

    ```
    create schema testdemo;
    
    IMPORT FOREIGN SCHEMA public_data LIMIT to(
      customer,
      customer_address,
      customer_demographics,
      inventory,item,
      date_dim,
      warehouse) 
      FROM server odps_server INTO testdemo options(if_table_exist 'update');
      
    set search_path to testdemo;
    ```

-   示例4：在public schema批创建外部表，已有外表则报错。

    ```
    IMPORT FOREIGN SCHEMA public_data LIMIT to
    (customer,
      customer_address) 
      FROM server odps_server INTO PUBLIC options(if_table_exist 'error');
    ```

-   示例5：在public schema批量创建外部表，已有外表则跳过该外部表。

    ```
    IMPORT FOREIGN SCHEMA public_data LIMIT to
    (customer,
      customer_address) 
      FROM server odps_server INTO PUBLIC options(if_table_exist 'ignore');
    ```


## 可视化批量创建外部表

支持通过HoloWeb可视化批量创建外部表，详情请参见[批量创建外部表](/intl.zh-CN/连接开发工具/HoloWeb/连接管理/MaxCompute加速/批量创建外部表.md)。

## 数据类型映射

批量创建的MaxCompute外部表与Hologres的数据类型映射，详情请参见[批量创建MaxCompute外部表与Hologres的数据类型映射](/intl.zh-CN/SQL参考/数据类型/数据类型汇总.md)。

