---
keyword: [target user identification function, Hologres]
---

# Target user identification functions

This topic describes how to use the bit\_construct and bit\_match functions for target user identification.

## Background information

In target user identification scenarios, you need to identify target users from tables. For example, a table contains multiple records of each user and different records meet different conditions. To identify target users, you need to query the users who meet a combination of specific conditions.

For example, query the users who have `clicked the shopping cart` and `viewed favorites` from the following sample table. In this example, User A meets the query conditions.

|User|Action|Page|
|----|------|----|
|A|click|Shopping cart|
|B|click|Homepage|
|A|view|Favorites|
|B|click|Shopping cart|
|A|click|Favorites|

Traditionally, you need to specify conditions to query data multiple times and use JOIN statements to obtain the results that meet the conditions. This method requires complex SQL statements. In addition, multiple JOIN statements consume a lot of resources. In Hologres V0.10 and later, Hologres provides the bit\_construct and bit\_match functions for target user identification. You need to specify conditions to query data only once and use the functions to obtain the results that meet the conditions. This simplifies data development in target user identification scenarios and helps you obtain the results with ease.

## Limits

When you use target user identification functions in Hologres, take note of the following limits:

-   Only Hologres V0.10 and later allow you to use target user identification functions. You can view the version of your Hologres instance in the Hologres console. If the version of your Hologres instance is earlier than V0.10, [submit a ticket](https://workorder-intl.console.aliyun.com/) to upgrade your instance.
-   Before you use the functions, you must execute the following statement to install an extension as the superuser. An extension is installed at the database level. For each database, you need to install an extension only once. If you create a database, you must execute the statement again.

    ```
    CREATE EXTENSION flow_analysis;
    ```


## bit\_construct

In Hologres V0.10 and later, Hologres provides the bit\_construct function for target user identification.

-   **Description**

    The bit\_construct function returns a bitmap of the INTEGER type based on the specified filter conditions. The return value can be up to 32 bits in length.

-   **Syntax**

    ```
    bit_construct(
      a ,
      b ,
      ....,
      a6
    )
    ```

-   **Parameters**
    -   The parameters such as a and b specify filter conditions whose return values are of the BOOLEAN type. A maximum of 32 conditions are supported. Valid values: a to z and a1 to a6.
    -   This function returns a value of the INT type.

## bit\_match

In Hologres V0.10 and later, Hologres provides the bit\_match function for target user identification.

-   **Description**

    The bit\_match function is used to further calculate the output of the bit\_construct function.

-   **Syntax**

    ```
    bit_match('expression', bitmask)
    ```

-   **Parameters**

    |Parameter|Description|Example|
    |---------|-----------|-------|
    |expression|The conditional expression in the bit\_construct function. The expression can contain the following operators and characters: AND \(`&`\), OR \(`|`\), NOT \(`!`\),XOR \(`^`\), and parentheses `()`.|a&b|
    |bitmask|The name of the bitmap that is constructed by the bit\_construct function.|N/A|


## Examples

The following sample code provides an example on how to use target user identification functions:

1.  Install an extension.

    The superuser of an instance can execute the following statement to install an extension in a database:

    ```
    CREATE EXTENSION flow_analysis;
    ```

2.  Prepare a table and data.

    Create a table that records user IDs and the events in the online shopping process of users. Then, insert data into the table. Use the following sample statements:

    ```
    create table ods_app_dwd(
    event_time timestamptz,
    uid bigint,
    action text,
    page text,
    product_code text,
    from_days int
    );
    
    insert into ods_app_dwd values('2021-04-03 10:01:30', 274649163, 'click', 'Shopping cart', 'MDS', 1);
    insert into ods_app_dwd values('2021-04-03 10:04:30', 274649163, 'view', 'Favorites', 'MDS', 4);
    insert into ods_app_dwd values('2021-04-03 10:06:30', 274649165, 'click', 'Shopping cart', 'MMS', 8);
    insert into ods_app_dwd values('2021-04-03 10:09:30', 274649165, 'view', 'Shopping cart', 'MDS', 10);
    ```

3.  Query data.

    Query the users who added a product to the shopping cart and to favorites within a specific period of time.

    You can query data by using one of the following methods:

    -   Use the WHERE clause.

        **Note:** The less data that is queried by using the WHERE clause indicates better query performance.

        ```
        WITH tbl as (
        SELECT uid, bit_or(bit_construct(
          a := (action='click' and page='Shopping cart'),
          b := (action='view' and page='Favorites'))) as uid_mask
          FROM ods_app_dwd
        WHERE event_time > '2021-04-03 10:00:00' AND event_time < '2021-04-04 10:00:00'
        GROUP BY uid )
        SELECT uid from tbl where bit_match('a&b', uid_mask);
        ```

        Functions in the SQL statements:

        -   bit\_construct: uses the Click the shopping cart event as Condition a and the View favorites event as Condition b to query the users who meet the filter conditions.
        -   bit\_or: performs the bitwise OR operation on the users who meet Condition a and the users who meet Condition b. In this example, if a record of a user meets Condition a or b, the user meets the filter conditions.
        -   bit\_match: uses the a&b expression to query the users who meet both Condition a and Condition b.
    -   Use the HAVING clause.

        ```
        SELECT uid FROM (
            SELECT uid, bit_or(bit_construct(
              a := (action='click' AND page='Shopping cart'),
              b := (action='view' AND page='Favorites'))) as uid_mask
            FROM ods_app_dwd
            WHERE event_time > '2021-04-03 10:00:00' AND event_time < '2021-04-04 10:00:00'
            GROUP BY uid 
            HAVING bit_match('a&b', bit_or(bit_construct(
              a := (action='click' and page='Shopping cart'),
              b := (action='view' and page='Favorites'))))
        ) t
        ```


