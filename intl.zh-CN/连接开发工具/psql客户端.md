---
keyword: [Hologres, psql客户端]
---

# psql客户端

本文为您介绍psql客户端如何连接交互式分析Hologres，并使用标准的PostgreSQL语句进行数据开发。

1.  您需要进入[Postgres官网](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)，下载与电脑系统相匹配的PostgreSQL 11及以上版本的客户端安装包，并根据提示安装。
2.  设置环境变量，具体如下：

    -   Windows系统。
        1.  在**系统属性** \> **高级系统设置**界面，单击**环境变量**。
        2.  添加PostgreSQL的bin文件路径至Path中。
        3.  单击**确定**。
    -   设置MacOS系统的环境变量，请参见[设置环境变量](https://www.postgresql.org/docs/6.3/c0301.htm)。
    **说明：** Linux系统无需设置环境变量。


1.  进入psql客户端命令行界面，输入连接信息。

    -   Linux系统语句如下。

        ```
        psql -h <Endpoint> -p <Port> -U <AccessKey ID> -d <Database>
        ```

        执行完上述语句后，您需要输入AccessKey Secret。

        ![LIN](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3162459951/p143892.png)

    -   MacOS系统语句如下。

        ```
        PGUSER=<AccessKey ID> PGPASSWORD=<AccessKey Secret> psql -p <Port> -h <Endpoint> -d <Database>
        ```

        ![linux](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3162459951/p137096.png)

    -   Windows系统语句如下。

        ```
        Server [localhost]: Endpoint
        Database [postgres]: Database
        Port [5432]: Port
        Username [postgres]: <AccessKey ID>
        用户 <AccessKey ID> 的口令：<AccessKey Secret>
        ```

        ![连接psql](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3162459951/p137051.png)

    |参数|描述|
    |--|--|
    |AccessKey ID|当前阿里云账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。 |
    |AccessKey Secret|当前阿里云账号的AccessKey Secret。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。 |
    |Port|Hologres实例的公共网络端口。示例取值`80`。 |
    |Endpoint|Hologres实例的公共网络地址。示例取值`xxx-cn-hangzhou.hologres.aliyuncs.com`。 |
    |Database|Hologres的数据库名称。开通Hologres实例后，系统自动创建**postgres**数据库。

您可以使用**postgres**数据库链接Hologres，但是该数据库分配到的资源较少，开发实际业务建议您新建数据库。详情请参见[创建数据库](/intl.zh-CN/快速入门/psql快速入门.md)。

示例取值`mydb`。 |


成功连接Hologres后，您可以使用标准的PostgreSQL语句，在psql客户端创建数据库，详情请参见[创建数据库](/intl.zh-CN/快速入门/psql快速入门.md)。

