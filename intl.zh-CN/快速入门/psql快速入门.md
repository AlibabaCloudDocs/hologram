---
keyword: [Hologres, 基本流程, 快速入门, psql客户端]
---

# psql快速入门

本文为您介绍如何通过psql客户端连接并使用交互式分析Hologres，帮助您快速掌握Hologres的基本使用流程。

Hologres兼容PostgreSQL，与大数据生态无缝连接，支持直接查询分析MaxCompute的数据，支持高并发地写入和查询实时数据，帮助您快速构建企业实时数据仓库。

本实验以通过psql客户端使用Hologres为例，为您介绍Hologres的基本使用流程。

您也可以使用HoloWeb，以可视化的方式完成实验。

1.  开通实例。

    1.  登录[阿里云官网](https://www.alibabacloud.com/)。

    2.  进入[Hologres产品详情页](https://www.alibabacloud.com/zh/product/hologres)购买实例。详情请参见[计费方式](/intl.zh-CN/产品定价/计费方式.md)。

2.  查看实例信息。

    1.  进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)。

    2.  在**Hologres引擎管理**页面，单击目标实例的名称，进入实例详情页查看实例的详细信息。

        您也可以单击目标实例**操作**列的**管理**，进入实例详情页。

3.  连接psql客户端，详情请参见[psql客户端](/intl.zh-CN/连接开发工具/psql客户端.md)。

4.  创建数据库。

    开通Hologres实例后，系统自动创建**postgres**数据库。该数据库分配到的资源较少，仅用于管理，开发实际业务建议您新建数据库。

    示例SQL语句如下。

    ```
    CREATE Database NewDatabaseName;
    CREATE Database test; //示例创建一个名为test的数据库。
    ```

5.  授权子账号。

    1.  创建子账号，详情请参见[创建RAM用户](/intl.zh-CN/准备工作/RAM用户使用Hologres.md)。如果您已创建子账号，请跳过该步骤。

    2.  授予子账号实例的开发权限，详情请参见[授予RAM用户实例的开发权限](/intl.zh-CN/账号与权限管理/授权操作/授予RAM用户实例的开发权限.md)。

        如果子账号是普通用户Normal，则子账号被授予实例的开发权限后才能访问Hologres。如果子账号已经被授权为Superuser，请跳过该步骤，直接进行数据开发。

6.  数据开发。

    使用标准的PostgreSQL语句，在psql客户端进行数据开发。

    示例在数据库中创建一张表并写入数据，SQL语句如下。

    ```
    BEGIN;
    CREATE TABLE nation (
     n_nationkey bigint NOT NULL,
     n_name text NOT NULL,
     n_regionkey bigint NOT NULL,
     n_comment text NOT NULL,
    PRIMARY KEY (n_nationkey)
    );
    CALL SET_TABLE_PROPERTY('nation', 'bitmap_columns', 'n_nationkey,n_name,n_regionkey');
    CALL SET_TABLE_PROPERTY('nation', 'dictionary_encoding_columns', 'n_name,n_comment');
    CALL SET_TABLE_PROPERTY('nation', 'time_to_live_in_seconds', '31536000');
    COMMIT;
    
    INSERT INTO nation VALUES 
    (11,'zRAQ', 4,'nic deposits boost atop the quickly final requests? quickly regula'),
    (22,'RUSSIA', 3  ,'requests against the platelets use never according to the quickly regular pint'),
    (2,'BRAZIL',  1 ,'y alongside of the pending deposits. carefully special packages are about the ironic forges. slyly special '),
    (5,'ETHIOPIA',  0 ,'ven packages wake quickly. regu'),
    (9,'INDONESIA', 2  ,'slyly express asymptotes. regular deposits haggle slyly. carefully ironic hockey players sleep blithely. carefull'),
    (14,'KENYA',  0  ,'pending excuses haggle furiously deposits. pending, express pinto beans wake fluffily past t'),
    (3,'CANADA',  1 ,'eas hang ironic, silent packages. slyly regular packages are furiously over the tithes. fluffily bold'),
    (4,'EGYPT', 4 ,'y above the carefully unusual theodolites. final dugouts are quickly across the furiously regular d'),
    (7,'GERMANY', 3 ,'l platelets. regular accounts x-ray: unusual, regular acco'),
    (20 ,'SAUDI ARABIA',  4 ,'ts. silent requests haggle. closely express packages sleep across the blithely');
    
    SELECT * FROM nation;
    ```

    您也可以使用HoloWeb进行数据开发，详情请参见[HoloWeb快速入门](/intl.zh-CN/快速入门/HoloWeb快速入门.md)。

    更多关于Hologres的数据开发，请参见[通过创建外部表加速查询MaxCompute数据](/intl.zh-CN/数据接入/大数据/MaxCompute/通过创建外部表加速查询MaxCompute数据.md)或[实时计算实时写入数据至Hologres]()。


