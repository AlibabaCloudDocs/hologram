# Roaring Bitmap函数

本文将为您介绍在Hologres中Roaring Bitmap函数的具体参数和使用。

## 背景信息

Roaring Bitmap是一种高效的Bitmap压缩算法，目前已被广泛应用在各种语言和各种大数据平台。适合计算超高基维的，常用于去重、标签筛选、时间序列等计算中。

Roaring Bitmap算法是将32位的INT类型数据划分为216个数据块（Chunk），每一个数据块对应整数的高16位，并使用一个容器（Container）来存放一个数值的低16位。Roaring Bitmap将这些容器保存在一个动态数组中，作为一级索引。容器使用两种不同的结构：数组容器（Array Container）和位图容器（Bitmap Container）。数组容器存放稀疏的数据，位图容器存放稠密的数据。如果一个容器里面的整数数量小于4096，就用数组容器来存储值。若大于4096，就用位图容器来存储值。

采用这种存储结构，Roaring Bitmap可以快速检索一个特定的值。在做位图计算（AND、OR、XOR）时，Roaring Bitmap提供了相应的算法来高效地实现在两种容器之间的运算。使得Roaring Bitmap无论在存储和计算性能上都表现优秀。

## 使用限制

在Hologres中使用Roaring Bitmap函数，具体限制如下：

-   该函数仅Hologres V0.10及以上版本支持，请在Hologres管控台查看当前实例版本，如果您的实例是V0.10以下版本，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)升级实例。
-   Roaring Bitmap函数在使用之前，需要执行以下语句开启extension才可以调用。extension是DB级别的函数，一个DB只需执行一次即可，新建DB需要重新执行。

    ```
    CREATE EXTENSION roaringbitmap;
    ```


## Bitmap函数列表

Bitmap函数主要包括的函数及说明如所示：

|函数名|输入|输出|描述|示例|
|---|--|--|--|--|
|rb\_build|integer\[\]|roaringbitmap|通过数组创建一个Bitmap。|`rb_build('{1,2,3,4,5}')`|
|rb\_and|roaringbitmap，roaringbitmap|roaringbitmap|And计算。|`rb_and(rb_build('{1,2,3}'),rb_build('{3,4,5}'))`|
|rb\_or|roaringbitmap，roaringbitmap|roaringbitmap|Or计算。|`rb_or(rb_build('{1,2,3}'),rb_build('{3,4,5}'))`|
|rb\_xor|roaringbitmap，roaringbitmap|roaringbitmap|Xor计算。|`rb_xor(rb_build('{1,2,3}'),rb_build('{3,4,5}'))`|
|rb\_andnot|roaringbitmap，roaringbitmap|roaringbitmap|AndNot计算。|`rb_andnot(rb_build('{1,2,3}'),rb_build('{3,4,5}'))`|
|rb\_cardinality|roaringbitmap|integer|统计基数。|`rb_cardinality(rb_build('{1,2,3,4,5}'))`|
|rb\_and\_cardinality|roaringbitmap，roaringbitmap|integer|And计算并返回基数。|`rb_and_cardinality(rb_build('{1,2,3}'),rb_build('{3,4,5}'))`|
|rb\_or\_cardinality|roaringbitmap，roaringbitmap|integer|Or计算并返回基数。|`rb_or_cardinality(rb_build('{1,2,3}'),rb_build('{3,4,5}'))`|
|rb\_xor\_cardinality|roaringbitmap，roaringbitmap|integer|Xor计算并返回基数。|`rb_xor_cardinality(rb_build('{1,2,3}'),rb_build('{3,4,5}'))`|
|rb\_andnot\_cardinality|roaringbitmap，roaringbitmap|integer|AndNot计算并返回基数。|`rb_andnot_cardinality(rb_build('{1,2,3}'),rb_build('{3,4,5}'))`|
|rb\_is\_empty|roaringbitmap|boolean|判断是否为空的Bitmap。|`rb_is_empty(rb_build('{1,2,3,4,5}'))`|
|rb\_equals|roaringbitmap，roaringbitmap|boolean|判断两个Bitmap是否相等。|`rb_equals(rb_build('{1,2,3}'),rb_build('{3,4,5}'))`|
|rb\_intersect|roaringbitmap，roaringbitmap|boolean|判断两个Bitmap是否相交。|`rb_intersect(rb_build('{1,2,3}'),rb_build('{3,4,5}'))`|
|rb\_remove|roaringbitmap,integer|roaringbitmap|从Bitmap移除特定的Offset。|`rb_remove(rb_build('{1,2,3}'),3)`|
|rb\_flip|roaringbitmap,integer,integer|roaringbitmap|翻转Bitmap中特定的Offset段。|`rb_flip(rb_build('{1,2,3}'),2,3)`|
|rb\_minimum|roaringbitmap|integer|返回Bitmap中最小的Offset，如果Bitmap为空则返回-1。|`rb_minimum(rb_build('{1,2,3}'))`|
|rb\_maximum|roaringbitmap|integer|返回Bitmap中最大的Offset，如果Bitmap为空则返回0。|`rb_maximum(rb_build('{1,2,3}'))`|
|rb\_rank|roaringbitmap,integer|integer|返回Bitmap中小于等于指定Offset的基数。|`rb_rank(rb_build('{1,2,3}'),3)`|
|rb\_iterate|roaringbitmap|setof integer|返回Offset List。|`rb_iterate(rb_build('{1,2,3}'))`|

## Bitmap聚合函数列表

Bitmap聚合函数主要包括的函数及说明如所示：

|函数名|输入数据类型|输出数据类型|描述|示例|
|---|------|------|--|--|
|rb\_build\_agg|integer|roaringbitmap|将Offset聚合成bitmap。|`rb_build_agg(1)`|
|rb\_or\_agg|roaringbitmap|roaringbitmap|Or聚合计算。|`rb_or_agg(rb_build('{1,2,3}'))`|
|rb\_and\_agg|roaringbitmap|roaringbitmap|And聚合计算。|`rb_and_agg(rb_build('{1,2,3}'))`|
|rb\_xor\_agg|roaringbitmap|roaringbitmap|Xor聚合计算。|`rb_xor_agg(rb_build('{1,2,3}'))`|
|rb\_or\_cardinality\_agg|roaringbitmap|integer|Or聚合计算并返回其基数。|`rb_or_cardinality_agg(rb_build('{1,2,3}'))`|
|rb\_and\_cardinality\_agg|roaringbitmap|integer|And聚合计算并返回其基数。|`rb_and_cardinality_agg(rb_build('{1,2,3}'))`|
|rb\_xor\_cardinality\_agg|roaringbitmap|integer|Xor聚合计算并返回其基数。|`rb_xor_cardinality_agg(rb_build('{1,2,3}'))`|

## 使用示例

如下内容将为您介绍Roaring Bitmap函数完整的使用示例。

1.  加载RoaringBitmap函数插件。

    ```
    CREATE EXTENSION roaringbitmap;
    ```

2.  创建带有RoaringBitmap数据类型的表。

    ```
    --创建名称为t1的表
    CREATE TABLE public.t1 (id integer, bitmap roaringbitmap);
    ```

3.  使用rb\_build函数插入RoaringBitmap的数据。

    ```
    --数组位置对应的BIT值为1
    INSERT INTO public.t1 SELECT 1,RB_BUILD(ARRAY[1,2,3,4,5,6,7,8,9,200]);
    
    --将输入的多条记录的值对应位置的BIT值设置为1，最后聚合为一个RoaringBitmap  
    INSERT INTO public.t1 SELECT 2,RB_BUILD_AGG(e) FROM GENERATE_SERIES(1,100) e;
    ```

4.  进行Bitmap计算（OR、AND、XOR、ANDNOT）。

    ```
    SELECT  RB_OR(a.bitmap,b.bitmap)
    FROM    (
                SELECT  bitmap
                FROM    public.t1
                WHERE   id = 1
            ) AS a
            ,(
                SELECT  bitmap
                FROM    public.t1
                WHERE   id = 2
            ) AS b
    ;
    ```

5.  进行Bitmap聚合计算（OR、AND、XOR、BUILD），并生成新的RoaringBitmap类型。

    ```
    SELECT RB_OR_AGG(bitmap) FROM public.t1;
    SELECT RB_AND_AGG(bitmap) FROM public.t1;
    SELECT RB_XOR_AGG(bitmap) FROM public.t1;
    SELECT RB_BUILD_AGG(id) FROM public.t1;
    ```

6.  统计基数（Cardinality），即统计RoaringBitmap中包含多少个位置为1的BIT位。

    ```
    SELECT RB_CARDINALITY(bitmap) FROM public.t1;
    ```

7.  从RoaringBitmap中返回位置为1的BIT下标（即位置值）。

    ```
    SELECT RB_ITERATE(bitmap) FROM public.t1 WHERE id = 1;
    ```


