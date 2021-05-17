# Shard级别的Replication使用（Beta）

本文将为您介绍在Hologres中Shard级别的Replication使用。

## 功能概述

从Hologres V0.10版本开始，Hologres支持通过设置Table Group副本数的方式来提高某个Table Group点查服务的可用性。您可以在创建Table Group的时候通过显示指定的replica count或者修改已有的replica count来打开Replication的功能。

关于replica count的说明具体如下：

-   数据是按Shard分布的，不同Shard管理不同的数据，不同Shard之间的数据没有重复，所有的Shard在一起是一份完整的数据。
-   默认情况下每一个Shard只有一个副本，即`replica count = 1`，其对应的属性为leader。您可以通过调整replica count的值，使相同的数据有多个副本，其他副本的属性为follower。
-   所有读写请求都由leader shard负责，当leader shard不可用后，系统会自动切换到follower shard上进行查询（当您使用follower shard查询时，数据可能会出现10~20ms级别延迟）。
-   replica count默认值是1，表示不启用replication。大于1表示开启replication，建议设置为2，replica count数字越大，对资源的消耗也越大。

当查询服务出现超时（可能是网络超时、进程停止服务或者其他原因），用户的查询可以无感知的被切换到Replication的节点上进行高可用查询，目前高可用的场景仅支持行存表的基于主键的读取方式。

## 使用限制

在Hologres中使用Shard级别的Replication，具体限制如下：

-   该功能仅Hologres V0.10及以上版本支持，请在Hologres管控台的实例详情页查看当前实例版本，如果您的实例是V0.10以下版本，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)升级实例。
-   Shard级别的Replication功能仅适用于行存表基于主键查询的场景。

## 语法说明

-   **查询当前DB的Table Group**

    您可以使用如下语法查看当前DB有哪些Table Group。

    ```
    select * from hologres.hg_table_group_properties ;
    ```

-   **查询已有Table Group的replica count**
    -   语法示例

        ```
        select property_value from hologres.hg_table_group_properties where tablegroup_name = 'table_group_name' and property_key = 'replica_count';
        ```

    -   参数说明

        |参数|说明|
        |--|--|
        |table\_group\_name|请输入您需要查询的Table Group名称。|
        |replica\_count|此处为固定参数名称，无需修改。|

-   **开启Replication**
    -   语法示例
        -   开启read replica功能，每个DB执行一次。您需要将`<databasename>`替换为实际的DB名称。

            ```
             alter database <databasename> set hg_experimental_enable_read_replica = on;
            ```

        -   为Table Group开启Replication。

            ```
            call hg_set_table_group_property ('table_group_name', 'replica_count', '2');
            ```

    -   参数说明

        |参数|说明|
        |--|--|
        |hg\_experimental\_enable\_read\_replica|是否开启read replica功能，每个DB执行一次即可。        -   on：开启read replica功能。
        -   off：关闭read replica功能。 |
        |hg\_set\_table\_group\_property|修改Table Group的replica\_count。        -   table\_group\_name：请输入您需要修改的Table Group名称。
        -   replica\_count：设置目标Table Group的副本数量。
        -   设置是否开启Replication：1为默认值，表示不启用read replica功能。大于1的数值表示启用read replica功能。 |

-   **高可用查询**

    当前功能仅支持行存表基于主键查询的场景，查询语法示例如下：

    ```
    -- 假设表主键为pk1和pk2
    select * from your_table_name where pk1 = 'xx' and pk2 = 'xxx';
    ```

    其中，`your_table_name`需要替换为您的目标表名称，pk1和pk2取值为实际的主键名称。

-   **高可用切换**
    -   语法示例

        ```
        -- 开启read replica功能
        set hg_experimental_enable_read_replica = on;
        
        -- 请求leader服务的超时时间(毫秒)，默认60ms，超时未返回结果即切换到replica。
        set hg_experimental_read_replica_leader_timeout_ms = 60;
        
        -- leader服务超时后，持续读replica的最大时间(秒)，默认60秒，超过时间会尝试切回leader继续查询。
        set hg_experimental_read_replica_max_duration_sec = 30; 
                                    
        ```

    -   参数说明

        |参数|说明|
        |--|--|
        |hg\_experimental\_enable\_read\_replica|是否开启read replica功能，每个DB执行一次即可。        -   on：开启read replica功能。
        -   off：关闭read replica功能。 |
        |hg\_experimental\_read\_replica\_leader\_timeout\_ms|请求leader shard服务的超时时间（毫秒），默认为60ms，超时未返回结果即切换到replica。|
        |hg\_experimental\_read\_replica\_max\_duration\_sec|leader shard服务超时后，持续读replica的最大时间（秒），默认为60s，每隔一个max\_duration时间，系统会尝试切回leader shard继续查询，完成服务的自动切换。|

-   **关闭Replication**
    -   语法示例

        ```
         -- 修改replica_count，关闭replication
        call hg_set_table_group_property ('table_group_name', 'replica_count', '1');
        ```

    -   参数说明

        |参数|说明|
        |--|--|
        |hg\_set\_table\_group\_property|修改Table Group的replica\_count。        -   table\_group\_name：请输入您需要修改的Table Group名称。
        -   replica\_count：设置目标Table Group的副本数量。
        -   设置是否开启Replication：1为默认值，表示不启用read replica功能。大于1的数值表示启用read replica功能。 |


