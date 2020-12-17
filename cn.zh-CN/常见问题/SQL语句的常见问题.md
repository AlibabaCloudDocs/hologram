# SQL语句的常见问题

## 如何处理数字开头的字段？

交互式分析Hologres兼容PostgreSQL，使用语法同PostgreSQL，不支持数字开头的字段。

如果您使用Hologres时遇到数字开头的字段，查询时需要为该字段增加双引号，示例如下。

```
select bizdate,"1_day_active_users","7_day_active_users" from t_active_users;
```

## 执行TRUNCATE语句报错

-   问题现象

    执行`TRUNCATE TABLE <table name>;`语句，出现`ERROR: TRUNCATE TABLE is not supported now.`报错。

-   问题原因

    当前使用的Hologres版本较低。

-   解决方法

    请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)，升级Hologres至0.8及以上版本。


## 执行INSERT ON CONFLICT语句报错

-   问题现象

    对数据源执行`INSERT ON CONFLICT`语句时出现如下报错。

    ![insert on conflict 常见报错](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5905108061/p200644.png)

-   问题原因

    Hologres兼容PostgreSQL，使用的也是标准PostgreSQL语法。在标准的PostgreSQL语义中，对数据源执行`INSERT ON CONFLICT`语句时，数据源不能包含重复数据，如果包含重复数据则会产生上述报错。

    **说明：** 数据源重复是指待插入的数据中包含重复数据，不是指待插入的数据与表里的数据重复。

    使用`INSERT ON CONFLICT`语句插入数据时包含重复数据，示例语句如下。

    ```
    insert into tmp1_on_conflict values(1,2,3),(1,2,3) on conflict(a) do update set (a, b ,c )= ROW(excluded.*);
    ```

-   解决方法

    如果数据源包含重复数据，可以配置`set hg_experimental_affect_row_multiple_times_keep_first = on;`，保留重复数据的第一条数据。


