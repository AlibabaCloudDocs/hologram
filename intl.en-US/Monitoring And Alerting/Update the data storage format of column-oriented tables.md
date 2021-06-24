# Update the data storage format of column-oriented tables

For Hologres instances of V0.10 or later, the data storage format of column-oriented tables is updated to AliORC. AliORC provides better performance in terms of the volume of stored data and thus can reduce the storage cost. This topic shows you how to update the data storage format of existing column-oriented tables in Hologres.

## Limits

Take note of the following rules when you update the data storage format:

-   This update is supported only by Hologres V0.10 and later. You can check the version of your Hologres instance on the instance details page in the Hologres console. If your instance version is earlier than V0.10, [submit a ticket](https://workorder-intl.console.aliyun.com/) to upgrade your instance.
-   The data storage format of existing column-oriented tables is segment and that of row-oriented tables is sst. For column-oriented tables that are newly created in Hologres instances of V0.10 or later, the updated data storage format, AliORC, is adopted. The field value orc indicates the AliORC format. The current version of Hologres allows you to update the data storage format of existing column-oriented tables but the following limits are imposed:
    -   You can update the data storage format of only a single column-oriented table at a time. Batch operations are not supported.
    -   If a column-oriented table is a partitioned table and you want to update its data storage format, you need to update the data storage format of the relevant parent table.
    -   After you perform the update operation, data in the previous storage format is asynchronously switched to the updated storage format by using background threads. **Such an asynchronous switch performed in the background consumes system resources, such as CPU, I/O, and network resources**. **During the switch, the performance of online tasks may be affected**. In addition, the time required for the switch varies based on the size of existing data and the size of data written in real time. Therefore, **update the data storage format during off-peak hours**.

## Query the data storage format

You can execute the following SQL statement to query the data storage format of each table in the specified database.

-   Sample statement

    ```
    SELECT  *
    FROM    hologres.hg_table_properties
    WHERE   property_key = 'storage_format'
    ;
    ```

-   Query results

    ```
     table_namespace |     table_name     |  property_key  | property_value 
    -----------------+--------------------+----------------+----------------
     public          | part               | storage_format | segment
     public          | partsupp           | storage_format | segment
     public          | customer           | storage_format | segment
     public          | orders_row         | storage_format | sst
     public          | sp_orders          | storage_format | segment
     public          | sp_orders_20161231 | storage_format | segment
     public          | sp_orders_20171231 | storage_format | segment
     public          | sp_orders_20181231 | storage_format | segment
     public          | lineitem           | storage_format | segment
     public          | nation             | storage_format | orc
     public          | region             | storage_format | orc
     public          | supplier           | storage_format | orc
     public          | orders             | storage_format | orc
    (13 rows)
    ```

-   Description of field values

    |Field value|Description|
    |-----------|-----------|
    |segment|The previous data storage format of column-oriented tables in Hologres instances of versions earlier than V0.10.|
    |sst|The default data storage format of row-oriented tables.|
    |orc|The default data storage format of column-oriented tables in Hologres instances of V0.10 and later. If the value of the property\_value field is orc, the table adopts the AliORC format.|


## Update the data storage format

After you update your Hologres instance to V0.10, newly created column-oriented tables in the instance adopt the AliORC format. However, you must execute the following statement to update the data storage format of existing column-oriented tables:

```
call set_table_property ('table_name', 'storage_format', 'orc');
```

Replace table\_name with the name of a table as needed. For example, you can specify the table name part. After you execute the following statement, the data storage format of the part table is updated to AliORC.

```
call set_table_property ('public.part', 'storage_format', 'orc');
```

