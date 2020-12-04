---
keyword: [聚合函数, APPROX\_COUNT\_DISTINCT, Hologres]
---

# APPROX\_COUNT\_DISTINCT

APPROX\_COUNT\_DISTINCT是聚合函数。本文为您介绍在交互式分析Hologres中APPROX\_COUNT\_DISTINCT函数的用法。

## 语法

`APPROX_COUNT_DISTINCT`函数用于计算某一列去重后的行数，结果只能返回一个值，并且该值为近似值。

```
APPROX_COUNT_DISTINCT ( column )
```

参数说明如下表所示。

|参数|描述|
|--|--|
|column|需要近似计算去重后行数的列。|

您可以通过HyperLogLog基数估计的方式进行非精确的COUNT DISTINCT计算。非精确的COUNT DISTINCT计算能提升查询性能，尤其是对于column的离散值比较大的情况，误差率平均可以控制在`0.1%`以内。该函数适用于对性能敏感并且可以接受误差的场景。

同时，您也可以通过COUNT DISTINCT \( column \)的方式进行精确的COUNT DISTINCT计算。

## 示例

计算O\_CUSTKEY列去重后行数的近似值，示例语句如下。

```
SELECT APPROX_COUNT_DISTINCT ( O_CUSTKEY ) FROM ORDERS;
```

