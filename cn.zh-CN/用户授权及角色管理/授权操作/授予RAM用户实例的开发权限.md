---
keyword: [子账号, 授权, 实例开发, RAM用户]
---

# 授予RAM用户实例的开发权限

本文为您介绍主账号如何使用简单权限模型和专家权限模型，授予RAM用户实例的开发权限。

系统默认设置购买实例的主账号为超级管理员Superuser。Superuser拥有该实例的所有权限，例如创建数据库、删除数据库、创建角色以及为角色授权等。

RAM用户的权限说明如下：

-   RAM用户需要被主账号授权后才能访问实例。RAM用户也可以被授权为Superuser。
-   即使RAM用户拥有实例的购买权限，也必须经过主账号授予实例的开发权限后，才能在Hologres实例中进行数据开发。

RAM权限与实例的开发权限的权限控制不同，详请参见[授予RAM用户权限](/cn.zh-CN/用户授权及角色管理/授权操作/授予RAM用户权限.md)。

## 使用简单权限模型为RAM用户授权（推荐）

您可以在[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)使用可视化方式为RAM用户授权。具体操作如下：

1.  新建用户。

    1.  使用主账号登录[阿里云官网](https://www.aliyun.com)。

    2.  登录[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)，单击目标实例名称，进入实例详情页。

    3.  在实例详情页的左侧导航栏，单击**用户管理**。

    4.  在**用户管理**页面，单击**新增用户**。

    5.  配置**新增用户**弹框的各项参数。

        ![新增用户](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8604158951/p133440.png)

        |参数|描述|
        |--|--|
        |选择组织成员|选择需要授权的RAM用户，将其创建至实例中。|
        |用户类型|        -   实例超级管理员\(superuser\)：拥有实例内所有操作的权限。
        -   普通用户：默认无实例的操作权限。

您需要授予RAM用户具体的操作权限后，RAM用户才能连接Hologres实例并使用。 |

    6.  单击**确定**。

2.  为用户授权。

    如果新增的用户是普通用户，则需要执行该步骤。具体操作如下：

    1.  在实例详情页的左侧导航栏，单击**DB管理**。

    2.  在**DB管理**页面，单击目标数据库操作列的**用户授权**。

        ![用户授权](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8604158951/p133464.png)

        如果**DB管理**页面没有数据库，您需要执行如下操作：

        1.  单击**新增Database**。
        2.  配置**Database名称**，并选择**简单权限模型**为**开启**。
    3.  进入数据库的权限管理页面，单击**新增授权**。

    4.  配置**新增授权**的各项参数。

        ![授权](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8604158951/p133483.png)

        |参数|描述|
        |--|--|
        |被授权账号|需要授权的RAM用户。|
        |用户组|        -   Admin：可以访问或操作数据库的所有对象，以及管理数据库的用户组。
        -   Developer：可以使用DDL语句创建、删除或修改数据库的对象，以及读写数据库对象中的数据。
        -   Writer：读写数据库对象中的数据。
        -   Viewer：拥有所有数据库对象的只读权限。 |

    5.  单击**确定**。

    简单权限模型也支持使用SQL语句为RAM用户授权，详情请参见[简单权限模型的使用](/cn.zh-CN/用户授权及角色管理/简单权限模型/简单权限模型的使用.md)。


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

    专家权限模型的更多授权操作请参见[专家权限模型](/cn.zh-CN/用户授权及角色管理/专家模式授权.md)。


## RAM用户使用Hologres

完成授权的RAM用户可以在Psql客户端连接Hologres实例并使用。详情请参见[psql客户端](/cn.zh-CN/连接开发工具/psql客户端.md)。

示例语句如下。

```
PGUSER=<AccessID> PGPASSWORD=<AccessKey> psql -p <Port> -h <Endpoint> -d <Database>
```

## 查看RAM用户的权限

您可以通过如下方式查看RAM用户的权限：

-   使用管理控制台查看RAM用户的权限。

    1.  进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页。
    2.  在实例详情页左侧导航栏，单击**用户管理**。
    3.  查看RAM用户的权限。
    如果您的数据库已经开启简单权限模型，您可以在**DB管理**页面，单击目标数据库的**用户授权**，查看RAM用户已加入的用户组。

-   使用SQL语句查看RAM用户的权限。

    连接开发工具至Hologres实例后，您可以使用如下SQL语句查看RAM用户的权限。

    ```
    SELECT * FROM pg_roles WHERE rolname = 'p4_ID'; //查看某个成员拥有的角色。
    SELECT rolname FROM pg_roles;
    SELECT user_display_name(rolname) FROM pg_roles;
    ```


