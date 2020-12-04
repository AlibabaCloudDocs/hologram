---
keyword: [aggregate functions, APPROX\_COUNT\_DISTINCT, Hologres]
---

# APPROX\_COUNT\_DISTINCT

APPROX\_COUNT\_DISTINCT is an aggregate function. This topic describes the syntax of the APPROX\_COUNT\_DISTINCT function in Hologres.

## Syntax

The `APPROX_COUNT_DISTINCT` function is invoked to calculate the number of rows that have distinct values in a column of a table. The result of this function is an approximate value.

```
APPROX_COUNT_DISTINCT ( column )
```

The following table describes the parameter in the syntax.

|Parameter|Description|
|---------|-----------|
|column|The column for which you want to calculate the approximate number of rows that have distinct values.|

You can use HyperLogLog \(HLL\) to perform an inaccurate COUNT DISTINCT operation. An inaccurate COUNT DISTINCT operation allows you to improve query performance especially when a large number of discrete values exist in the column. HLL ensures that the average error rate does not exceed `0.1%`. The APPROX\_COUNT\_DISTINCT function applies to scenarios where high query performance is required and an approximate result is acceptable.

You can also perform a precise COUNT DISTINCT operation by using the COUNT DISTINCT \(column\) function.

## Example

Execute the following statement to calculate the approximate number of rows that have distinct values in the O\_CUSTKEY column:

```
SELECT APPROX_COUNT_DISTINCT ( O_CUSTKEY ) FROM ORDERS;
```

