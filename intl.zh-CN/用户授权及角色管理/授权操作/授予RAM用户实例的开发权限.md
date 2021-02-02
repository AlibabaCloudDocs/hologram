---
keyword: [子账号, 授权, 实例开发, RAM用户]
---

# 授予RAM用户实例的开发权限

本文为您介绍主账号如何使用简单权限模型和专家权限模型，授予RAM用户实例的开发权限。

系统默认设置购买实例的主账号为超级管理员Superuser。Superuser拥有该实例的所有权限，例如创建数据库、删除数据库、创建角色以及为角色授权等。

RAM用户的权限说明如下：

-   RAM用户需要被主账号授权后才能访问实例。RAM用户也可以被授权为Superuser。
-   即使RAM用户拥有实例的购买权限，也必须经过主账号授予实例的开发权限后，才能在Hologres实例中进行数据开发。

RAM权限与实例的开发权限的权限控制不同，详请参见[授予RAM用户权限](/intl.zh-CN/用户授权及角色管理/授予RAM用户权限.md)。

## 使用简单权限模型为RAM用户授权（推荐）

1.  为用户授权。

    简单权限模型也支持使用SQL语句为RAM用户授权，详情请参见[简单权限模型的使用](/intl.zh-CN/用户授权及角色管理/Hologres权限模型/简单权限模型/简单权限模型的使用.md)。


## 使用专家权限模型为RAM用户授权

使用专家权限模型为RAM用户授权的操作步骤如下：

1.  新建用户。

    创建RAM用户至Hologres实例。示例语句如下。

    ```
    CREATE USER "p4_账号ID"; //ID为阿里云RAM用户的UID。
    CREATE USER "p4_账号ID" SUPERUSER; //授予RAM用户Superuser权限。
    ```

2.  为用户授权。

    RAM用户需要被授予相应的权限后，才能访问权限范围内的对象。示例授权语句如下。

    ```
    GRANT SELECT ON TABLE TABLENAME TO "账号ID"; //授予RAM用户表的查看权限。
    GRANT SELECT,INSERT,UPDATE ON ALL TABLES IN SCHEMA PUBLIC TO "p4_账号ID"; //授予RAM用户所有表的新增、修改和查看权限。
    ```

    **说明：** 只有Superuser和表Owner才可以删除表。

    专家权限模型的更多授权操作请参见[专家权限模型](/intl.zh-CN/用户授权及角色管理/Hologres权限模型/专家权限模型.md)。


## RAM用户使用Hologres

完成授权的RAM用户可以在Psql客户端连接Hologres实例并使用。详情请参见[psql客户端](/intl.zh-CN/连接开发工具/psql客户端.md)。

示例语句如下。

```
PGUSER=<AccessID> PGPASSWORD=<AccessKey> psql -p <Port> -h <Endpoint> -d <Database>
```

## 查看RAM用户的权限

您可以通过如下方式查看RAM用户的权限：

-   使用SQL语句查看RAM用户的权限。

    连接开发工具至Hologres实例后，您可以使用如下SQL语句查看RAM用户的权限。

    ```
    SELECT * FROM pg_roles WHERE rolname = 'p4_ID'; //查看某个成员拥有的角色。
    SELECT rolname FROM pg_roles;
    SELECT user_display_name(rolname) FROM pg_roles;
    ```


