# Resharding（Beta）

Resharding功能用于修改Shard数后，自动实现Rebalance的功能，无需再重新建表导数据，即可将原来的表和数据Resharding到各Shard上，简化操作步骤，实现最优性能。本文将为您介绍，在Hologres中如何使用Resharding。

## 背景信息

在Hologres中，Shard用于提升数据处理的并发度，合理的设置Shard数，有利于提高查询或者写入的性能。一般情况下，Hologres实例默认的Shard数已经能满足大部分场景，无需再手动修改。

当实例扩容后，例如32core扩容到128core，该DB的Shard数不会随着扩容默认更改，因此建议您针对该DB增加Shard数，以获取更好的性能。如果是该实例下新建的DB，其Shard数为当前规格的默认数量。实例规格与Shard的相关描述，请参见[实例规格概述](/intl.zh-CN/实例管理/实例规格概述.md)。

当实例扩容或者缩容后，其之前DB的Shard数不会自动随之增加或减少，需要通过命令语句设置Shard数，并重新进行数据导入。Resharding功能用于修改Shard数后，自动实现Rebalance的功能，无需再重新建表导数据，即可将原来的表和数据Resharding到各Shard上，简化操作步骤，实现最优性能。

## 使用限制

-   仅Hologres V0.10 及以上版本支持Resharding，请前往Hologres管控台的实例详情页查看当前实例版本，如果您的实例是V0.10以下版本，请您[提交工单](https://workorder-intl.console.aliyun.com/)升级实例。
-   目前Resharding是单表级别，当使用Resharding时，需要停止该表的写入（如flink、数据集成等写入），对表的查询不受影响。
-   Resharding会消耗一定的资源，建议在业务低峰期处理。

## 操作步骤

在使用Resharding功能时，您可以按照如下步骤进行操作：

1.  **（可选）查看Table Group**

    Shard绑定在Table Group上，当您修改Shard数时，需要创建Table Group。在修改之前，可以通过以下语句查看已有Table Group是否存在目标Shard数。

    **说明：** 创建Table Group会占用系统固定资源，应限制Table Group的无序创建，通常一个实例不超过3个Table Group。

    ```
    select * from hologres.hg_table_group_properties;
    ```

    其执行结果如下所示，当前DB有一个名为`xx_tg_default`的Table Group，其Shard数为20，该Table Group下共有9张表（仅包含内部表）。

    ![查看shard](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6042828161/p262524.png)

2.  **修改Shard数**

    如果您当前Table Group的Shard数不满足需求，您可以通过新建一个Table Group来修改Shard数（不支持在原Table Group上直接修改）。

    -   语法示例

        ```
        call hg_create_table_group('table_group_name',shard_count);
        ```

    -   参数说明

        |参数|说明|示例|
        |--|--|--|
        |table\_group\_name|新的Table Group名，不能与原Table Group名重复。|new\_tg|
        |shard\_count|目标Shard数。|60|

    -   使用示例

        ```
        --创建一个名为new_tg的Table Group，其Shard数是60
        call hg_create_table_group('new_tg',60);
        ```

3.  **表迁移**

    修改Shard数后，需要将表迁移至新的Table Group。执行如下语句，无需重新建表和导数据，就能实现表的Auto Resharding。

    **说明：**

    -   表迁移时需要停止对该表的写入，查询不受影响。
    -   原Table Group的表全部迁移后，表将会被自动删除。若是因为业务需求需要建立多个Table Group，建议合理设置每个Table Group的Shard数。
    -   表迁移时分区表只需要操作父表即可。
    -   语法示例

        ```
        call hg_update_table_shard_count('table_name','table_group_name')
        ```

    -   参数说明

        |参数|说明|示例|
        |--|--|--|
        |table\_name|需要迁移的表名。一次命令仅支持单表迁移，若是有多个表，需要多次执行。分区表只需要操作父表即可。更多关于表的查询，请参见[（可选）查看Table Group](#section_3nz_ej4_s2d)。|new\_table|
        |table\_group\_name|新的Table Group名。|new\_tg|


