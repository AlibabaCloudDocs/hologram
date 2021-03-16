---
keyword: [Hologres, ordered-set aggregate functions]
---

# Ordered-set aggregate functions

Hologres is compatible with PostgreSQL and allows you to use standard PostgreSQL syntax for data development.

Hologres supports a subset of PostgreSQL functions. The following table describes the ordered-set aggregate functions that are supported by Hologres.

|Function|Description|Direct argument type|Aggregated argument type|Example|Return type|
|--------|-----------|--------------------|------------------------|-------|-----------|
|`mode() WITHIN GROUP (ORDER BY sort_expression)`|Returns the most frequent input value. If multiple values are equally frequent, the function returns the first value.|None|A sortable type|-   Example

`select mode() WITHIN GROUP (ORDER BY user_id ) from testtable;`

-   Result

`293890`


|Same as the sorting expression|
|`percentile_cont(fraction) WITHIN GROUP (ORDER BY sort_expression)`|Continuous percentile: returns a value that corresponds to the specified fraction in the ordering. If needed, the function interpolates a value between adjacent input items.|`double precision`|`double precision` or `interval`|-   Example

`select percentile_cont(0.5) WITHIN GROUP (ORDER BY cust_id) from testtable;`

-   Result

`1105639996.5`


|Same as the sorting expression|
|`percentile_cont(fractions) WITHIN GROUP (ORDER BY sort_expression)`|Multiple continuous percentile: returns an array of results that match the shape of the fractions parameter, with each non-null element replaced by the value that corresponds to that percentile.|`double precision[]`|`double precision` or `interval`|-   Example

`select percentile_cont(0.5) WITHIN GROUP (ORDER BY member_id) from testtable;`

-   Result

`96727903.5`


|Array of the same type as the sorting expression|
|`percentile_disc(fraction) WITHIN GROUP (ORDER BY sort_expression)`|Discrete percentile: returns the first input value whose position in the ordering equals or exceeds the specified percentile.|`double precision`|A sortable type|-   Example

`select percentile_disc(0.6) WITHIN GROUP (ORDER BY (impression_id) from testtable;`

-   Result

`0.0`


|Same as the sorting expression|
|`percentile_disc(fractions) WITHIN GROUP (ORDER BY sort_expression)`|Multiple discrete percentile: returns an array of results that match the shape of the fractions parameter, with each non-null element replaced by the input value that corresponds to that percentile.|`double precision[]`|A sortable type|-   Example

`select percentile_disc(0.6) WITHIN GROUP (ORDER BY (impr_id) from testtable;`

-   Result

`0.0`


|Array of the same type as the sorting expression|

