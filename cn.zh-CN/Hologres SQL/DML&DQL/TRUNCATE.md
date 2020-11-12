---
keyword: [TRUNCATE, Hologres]
---

# TRUNCATE

TRUNCATE语句用于清空目标表中的数据。本文为您介绍Hologres中TRUNCATE的用法。

## 使用限制

`TRUNCATE`语句的使用限制如下：

-   Hologres支持Sequence，但目前仅支持CONTINUE IDENTITY，不支持RESTART IDENTITY。
-   Hologres支持对普通表、分区父表及分区子表执行`TRUNCATE`语句。
-   Hologres不支持对外部表执行`TRUNCATE`语句。
-   Hologres 0.8及以上版本才支持`TRUNCATE`功能。如果您的Hologres版本较低，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)进行升级。

## 语法

`TRUNCATE`的语法格式如下。

```
TRUNCATE [ TABLE ] name [, ... ]
    [CONTINUE IDENTITY ] 
```

参数说明如下表所示。

|参数|描述|
|--|--|
|CONTINUE IDENTITY|不修改当前Sequence值。**说明：** Hologres目前仅支持CONTINUE IDENTITY。 |

## 使用示例

`TRUNCATE`的使用示例如下：

-   示例一：创建并导入数据至event表，查询表数据后，执行`TRUNCATE TABLE event;`语句清空event表中的所有数据。语句如下。

    ```
    CREATE TABLE event (
        id int,
        name text,
        tag text
    );
    INSERT INTO event (id,name,tag) values (23,'buy', 'num');
    
    SELECT * FROM event;
    
    TRUNCATE TABLE event;
    ```

-   示例二：创建并导入数据至event\_1表，查询表数据后，执行`TRUNCATE TABLE event_1 CONTINUE IDENTITY;`语句清空event\_1表中的所有数据，但不更改表数据的序列值。语句如下。

    ```
    CREATE TABLE event_1 (
        id serial,
        name text,
        tag text
    );
    
    INSERT INTO event_1 (name,tag) values ('buy', 'num');
    
    SELECT * FROM event_1;
    
    #默认为CONTINUE IDENTITY
    TRUNCATE TABLE event_1 CONTINUE IDENTITY;
    ```


## 常见问题

-   问题现象

    执行`TRUNCATE TABLE <table name>;`语句，出现`ERROR: TRUNCATE TABLE is not supported now.`报错。

-   问题原因

    当前使用的Hologres版本较低。

-   解决方法

    请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)，升级Hologres至0.8及以上版本。


