---
keyword: [向量Proxima, Hologres]
---

# 向量Proxima（Beta）

Proxima向量检索引擎是运用于大数据领域，实现向量近邻搜索的高性能软件库，相比Fassi等开源产品，稳定性和性能较好，提供了行业相关的基础方法模块，支持图像、视频及人脸等各种应用场景的搜索。交互式分析Hologres的向量查询功能与Proxima深度整合，为您提供高性能的向量查询服务。本文为您介绍在Hologres中如何使用向量计算功能。

## 使用限制

当前向量计算Proxima功能还处于Beta版本，不能保证生产的稳定性，不建议直接用于生产环境。如果在使用过程中有任何问题，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)反馈。

## 使用Proxima进行向量计算

使用Proxima进行向量计算的操作步骤如下：

1.  安装Proxima插件。

    Proxima是以一个extension的方式与Hologres连通，因此在使用之前需要执行如下命令安装Proxima插件。

    ```
    create extension proxima;
    ```

    Proxima插件是针对数据库级别使用的，一个数据库仅需要安装一次即可。

2.  向量查询。

    在Hologres中，向量使用FLOAT4类型的数组表示，向量查询的API场景如下：

    -   精确查询

        以`pm_xxx`开头的UDF均为精准查询。

        1.  使用精准的API来做Top N的查询，需要您在表中定义好对应的向量字段，示例语句如下。

            ```
            create table feature_tb (
                id bigint,
                feature float4[] check(array_ndims(feature) = 1 and array_length(feature, 1) = 4)
            );
            ```

        2.  查询Top N，示例语句如下。

            ```
            select pm_squared_euclidean_distance(feature, '{0.1,0.1,0.1,0.1}') as distance from feature_tb order by distance asc limit 10;
            ```

    -   近似查询

        以`pm_approx`开头的UDF均为近似查询。

        1.  使用近似查询的API来做Top N查询，您需要在表中定义好对应的向量字段，并为对应的字段设置Proxima索引，示例语句如下。

            ```
            begin;
            create table feature_tb (
                id bigint,
                feature float4[] check(array_ndims(feature) = 1 and array_length(feature, 1) = 4)
            );
            call set_table_property('feature_tb', 'proxima_vectors', '{"feature":{"algorithm":"Graph","distance_method":"Euclidean","builder_params":
            {"min_flush_proxima_row_count" : 1000}, "searcher_init_params":{}}}');
            end;
            ```

            参数描述如下表所示。

            |参数|描述|
            |--|--|
            |algorithm|用于指定构建向量索引的算法，目前仅支持Graph。|
            |distance\_method|用于定义构建向量索引使用的距离计算方法。Hologres目前仅支持如下三种距离计算函数：            -   Euclidean 开方的欧式距离
            -   SquaredEuclidean 不开方的欧式距离
            -   InnerProduct 内积距离 |

        2.  查询Top N，示例语句如下。

            **说明：** 近似查询中的第二个参数必须为常量值。

            ```
            select pm_approx_euclidean_distance(feature, '{0.1,0.2,0.3,0.4}') as distance from feature_tb order by distance asc limit 10 ; //计算内积的TOPK，此时建表语句中的proxima_vector参数的distance_method需要设置为Euclidean。
            select pm_approx_inner_product_distance(feature, '{0.1,0.2,0.3,0.4}') as distance from feature_tb order by distance desc limit 10 ; //计算内积的TOPK，此时建表语句中的proxima_vector参数的distance_method需要设置为InnerProduct。
            ```


## 使用示例

使用Proxima进行向量计算的示例语句如下。

```
create extension proxima;
begin;
create table feature_tb (
    id bigint,
    feature float4[] check(array_ndims(feature) = 1 and array_length(feature, 1) = 4)
);
call set_table_property('feature_tb', 'proxima_vectors', '{"feature":{"algorithm":"Graph","distance_method":"InnerProduct","builder_params":
{"min_flush_proxima_row_count" : 1000}, "searcher_init_params":{}}}');
end;
insert into feature_tb select i, array[random(), random(), random(), random()]::float4[] from generate_series(1, 10000) i;
select pm_approx_inner_product_distance(feature, '{0.1,0.2,0.3,0.4}') as distance from feature_tb order by distance desc limit 10 ;
```

## 向量计算UDF

-   Hologres支持的三种向量距离评估函数如下：

    -   不开方的欧式距离，计算公式如下。

        ![不开方的欧式距离](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7161863061/p175758.png)

    -   开方的欧氏距离，计算公式如下。

        ![开方的欧氏距离](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7161863061/p175760.png)

    -   内积距离，计算公式如下。

        ![内积距离](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7161863061/p175762.png)

    **说明：** 如果您选用欧式距离进行向量计算，不开方的欧式距离与开方的欧式距离相比，可以少一个开方的计算，并且计算出的Top K记录一致。因此，不开方的欧式距离性能更好，在满足功能需求的情况下，一般建议您使用不开方的欧式距离。

-   Hologres支持的向量计算UDF如下：
    -   精准向量计算UDF

        ```
        float4 pm_euclidean_distance(float4[], float4[])
        float4 pm_squared_euclidean_distance(float4[], float4[])
        float4 pm_inner_product_distance(float4[], float4[])
        ```

    -   近似向量计算UDF

        ```
        float4 pm_approx_euclidean_distance(float4[], float4[])
        float4 pm_approx_squared_euclidean_distance(float4[], float4[])
        float4 pm_approx_inner_product_distance(float4[], float4[])
        ```


## 常见问题

-   报错`ERROR: function pm_approx_inner_product_distance(real[], unknown) does not exist`。

    原因：通常是因为未在数据库中执行`create extension proxima;`语句来初始化Proxima插件。

    解决方法：执行`create extension proxima;`语句初始化Proxima插件。

-   报错`Writting column: feature with array size: 5 violates fixed size list (4) constraint declared in schema`。

    原因：由于写入到特征向量列的数据维度与表中定义的维度数不一致，导致出现该报错。

    解决方法：可以排查下是否有胀数据。

-   报错`The size of two array must be the same in DistanceFunction, size of left array: 4, size of right array:`。

    原因：由于pm\_xx\_distance\(left, right\)中，left的维度与right的维度不一致所致。

    调整pm\_xx\_distance\(left, right\)中，left的维度与right的维度一致。


