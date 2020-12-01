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

    请[提交工单](https://workorder-intl.console.aliyun.com/)，升级Hologres至0.8及以上版本。


