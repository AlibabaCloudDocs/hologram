---
keyword: [Hologres, terms]
---

# Terms

This topic describes terms commonly used in Hologres, such as account, role, instance, database, schema, internal table, foreign table, and partitioned table.

## Account

-   Alibaba Cloud account

    An Alibaba Cloud account is used to create and manage Hologres instances. You can use an Alibaba Cloud account to grant permissions to RAM users.

    The following table describes the formats of Alibaba Cloud accounts in Hologres.

    |Format|Description|Required value|
    |------|-----------|--------------|
    |ALIYUN$<AccountName\>@aliyun.com|<AccountName\> indicates the logon name of an Alibaba Cloud account.|`ALIYUN$company@aliyun.com`|
    |<AccountName\>@aliyun.com|`company@aliyun.com`|
    |<Account ID\>|The ID of an Alibaba Cloud account.|`1344445678xxx`|

-   RAM user

    RAM users can create and manage objects, such as databases and tables, in an instance based on their permissions granted by Alibaba Cloud accounts.

    The following table describes the formats of RAM users in Hologres.

    |Format|Description|Required value|
    |------|-----------|--------------|
    |p4\_<Account ID\>|    -   In this format, p is in lowercase.
    -   <Account ID\> indicates the ID of the corresponding Alibaba Cloud account.
|`p4_12345678xxx`|
    |<subUserName\>@<Account Name\>.onaliyun.com|    -   <subUserName\> indicates the logon name of a RAM user.
    -   <AccountName\> indicates the logon name of the corresponding Alibaba Cloud account.
    -   <Account ID\> indicates the ID of the corresponding Alibaba Cloud account.
|`holouser@company.onaliyun.com`|
    |<subUserName\>@<Account Name\>|
    |<subUserName\>@<Account ID\>.onaliyun.com|
    |RAM$<subUserName\>|
    |RAM$<AccountName\>:<subUserName\>|
    |RAM$<Account ID\>:<subUserName\>|
    |<subUserName\>@<Account ID\>|


## Role

-   Superuser

    By default, the system sets the Alibaba Cloud account used to purchase an instance as a superuser of the instance. The superuser has all permissions on the instance. It can create databases, delete databases, create roles, and grant permissions to roles.

-   Normal user

    A normal user can log on to a Hologres instance and perform allowed data analytics operations only after the normal user obtains the required permissions from a superuser. A normal user can also be assigned the superuser role.


## Instance

In Hologres, an instance is an entity where you can use and manage databases. All database operations are performed in the instance.

You must purchase an instance before you use it. For more information about how to purchase an instance, see [t1877414.md\#](/intl.en-US/Preparations/Purchase a Hologres instance.md).

## Database

A database is a data repository that organizes, stores, and manages data based on data structures. In a database, you can create, update, and delete tables. You can also execute functions.

When you purchase a Hologres instance, a database named **postgres** is created by default. This database is allocated with a few resources and only used for management purposes. You can create a database based on your business needs. For more information about how to create a database, see [CREATE DATABASE](/intl.en-US/Hologres SQL/DDL/DATABASE/CREATE DATABASE.md).

## Schema

A schema is a collection of database objects, similar to a directory in a file system. Objects such as tables and functions in an instance are stored in schemas.

When you create a database, a schema named **public** is created by default. Operations on objects in Hologres instances are performed in this schema by default. You can also create a schema based on your business needs. For more information, see [CREATE SCHEMA](/intl.en-US/Hologres SQL/DDL/SCHEMA/CREATE SCHEMA.md).

**Note:**

-   A schema cannot contain nested subschemas.
-   Objects in a schema must have unique names.
-   Objects in different schemas can have the same name.

## Table

A table is a unit that stores data in columns and rows. The number and order of columns are fixed, and each column has a name. The number of rows is variable, which reflects the data amount stored at a given moment.

Each column has a data type. The data type constrains the set of possible values that can be assigned to a column. For more information, see [Data types](/intl.en-US/Hologres SQL/Data types/Data types.md).

Hologres supports the following two types of tables:

-   Internal table

    Internal tables store data in Hologres. The data types of internal tables must be supported by Hologres.

-   Foreign table

    Foreign tables map external data sources and do not store data in Hologres. Foreign tables are read-only. Therefore, you cannot perform data manipulation language \(DML\) operations or create indexes for foreign tables.

    You can create foreign tables in Hologres to query data directly from external data sources such as MaxCompute.

    For more information about how to create a foreign table in Hologres, see [CREATE FOREIGN TABLE](/intl.en-US/Hologres SQL/DDL/FOREIGN TABLE/CREATE FOREIGN TABLE.md).


## Partitioned table

Partitioned tables are parent tables divided into multiple segments based on partition keys. These segments are called partitions. Different types of data are stored in different partitions. In Hologres, only one partition key can be used for list partitioning. When you query data, you can specify filters based on partition key values in query statements to exclude irrelevant partitions. This improves the query speed.

For more information about how to create and delete a partitioned table in Hologres, see [CREATE/DROP PARTITION TABLE](/intl.en-US/Hologres SQL/DDL/PARTITION TABLE/CREATE/DROP PARTITION TABLE.md).

