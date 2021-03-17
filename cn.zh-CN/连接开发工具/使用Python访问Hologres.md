---
keyword: [Hologres, Python, Psycopg]
---

# 使用Python访问Hologres

Psycopg是Python用于操作PostgreSQL的库。Hologres兼容PostgreSQL 11，因此您可以通过Psycopg访问Hologres。本文将指导您使用Python访问Hologres，示例使用的操作环境为基于CentOS 7系统的Python 3.8版本。

## 安装Python3.8

您可以基于Miniconda、Anaconda安装Python 3.8环境。如下内容以CentOS 7系统为例，安装Python 3.8版本。

1.  安装Python 3.8。

    您可以下载对应版本的Python，执行如下命令进行安装。

    ```
    # yum install centos-release-scl
    # yum install rh-python38
    # scl enable rh-python38 bash
    # python --version
    Python 3.8.6
    ```

2.  安装psycopg2模块。

    执行如下命令安装psycopg2模块。

    ```
     # pip install psycopg2-binary
    ```


## 连接Hologres

Python3.8环境和Psycopg安装完成之后，您可以执行如下操作并连接Hologres。

1.  加载psycopg2。

    如果需要使用psycopg2，您可以执行命令`import psycopg2`加载安装的psycopy2。

2.  创建数据库连接。

    您可以使用`psycopg2.connect()`函数连接Hologres，具体语法和参数说明如下所示。

    ```
    conn = psycopg2.connect(host="<Endpoint>", port=<Port>, dbname="<databases>", user="<Access ID>", password="<Access Key>")
    ```

    |参数|描述|
    |--|--|
    |host|Hologres实例的网络地址。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取网络地址。 |
    |port|Hologres的实例端口。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取端口。 |
    |dbname|Hologres创建的数据库名称。|
    |user|当前阿里云账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。 |
    |password|当前阿里云账号的AccessKey Secret。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。 |


## 使用Hologres

当您成功连接Hologres数据库之后，即可通过psycopg2进行数据开发操作。如下内容将指导您创建表、插入数据、查询和释放资源等操作。

1.  创建游标。

    在进行数据开发之前，您需要执行命令`cur = conn.cursor()`来创建连接的游标。

2.  数据开发。

    1.  创建表

        您可以执行如下命令，创建一个表`holo_test`并定义表的数据类型为integer。您也可以根据业务需求定义表名称和数据类型。

        ```
        cur.execute("CREATE TABLE holo_test (num integer);")
        ```

    2.  插入数据

        您可以执行如下命令，为创建的表`holo_test`插入数据1~1000。

        ```
        cur.execute("INSERT INTO holo_test SELECT generate_series(%s, %s)", (1, 1000))
        ```

    3.  查询数据

        ```
        cur.execute("SELECT sum(num) FROM holo_test;")
        cur.fetchone()
        ```

3.  提交事务。

    在查询数据的命令之后，您需要执行命令`conn.commit()`提交事务，此操作可以确保操作已经提交。

4.  释放资源。

    为避免影响后续的操作，当操作执行完成后，您需要执行如下命令关闭游标并断开数据库连接。

    ```
    cur.close()
    conn.close()
    ```


