---
keyword: [Hologres, terms]
---

# Terms

This topic describes terms that are commonly used in Hologres, such as account, role, instance, database, schema, internal table, foreign table, and partitioned table.

## Account management

Hologres is deeply integrated with the Alibaba Cloud account system. Hologres accounts include Alibaba Cloud accounts and RAM users, as described in the following table. When you grant permissions on a Hologres instance to Alibaba Cloud accounts and RAM users, you must use their logon accounts or account IDs. For more information, see [\#d7e67](#d7e67) and [\#d7e168](#d7e168).

|Account type|Description|
|------------|-----------|
|Alibaba Cloud account|An Alibaba Cloud account is used to create and manage Hologres instances. For example, you can use your Alibaba Cloud account to log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance), create a database, change the billing method of an instance from pay-as-you-go to subscription, and grant permissions on objects to other users.|
|RAM user|After you use your Alibaba Cloud account to create a RAM user and grant the permissions to the RAM user, the RAM user can also create and manage Hologres instances. For example, the RAM user can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance), create a database, change the billing method of an instance from pay-as-you-go to subscription, and grant permissions on objects to other users.RAM users are subordinate to Alibaba Cloud accounts and do not possess actual resources. All resources belong only to Alibaba Cloud accounts. |

## role

-   superuser

    By default, the system sets the Alibaba Cloud account that is used to purchase an instance as a superuser of the instance. The superuser has all permissions on the instance. It can create databases, delete databases, create roles, and grant permissions to roles.

-   normal user

    A normal user can log on to a Hologres instance and perform allowed data analytics operations only after the normal user obtains the required permissions from a superuser. A normal user can also be assigned the superuser role.


## instance

In Hologres, an instance is an entity where you can use and manage databases. All database operations are performed in the instance.

You must purchase an instance before you use it. For more information about how to purchase an instance, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

## database

A database is a data repository that organizes, stores, and manages data based on data structures. In a database, you can create, update, and delete tables. You can also execute functions.

When you purchase a Hologres instance, a default database named **postgres** is created. This database is allocated with a few resources and is used only for management purposes. You can create a database based on your business needs. For more information about how to create a database, see [CREATE DATABASE](/intl.en-US/Hologres SQL/DDL/DATABASE/CREATE DATABASE.md).

## schema

A schema is a collection of database objects, similar to a directory in a file system. Objects such as tables and functions in an instance are stored in schemas.

When you create a database, a default schema named **public** is created. By default, operations on objects in Hologres instances are performed in this schema. You can also create a schema based on your business needs. For more information, see [CREATE SCHEMA](/intl.en-US/Hologres SQL/DDL/SCHEMA/CREATE SCHEMA.md).

**Note:**

-   A schema cannot contain nested subschemas.
-   Objects in a schema must have unique names.
-   Objects in different schemas can have the same name.

## table

A table is a unit that stores data in columns and rows. The number and order of columns are fixed, and each column has a name. The number of rows is variable, which reflects the data amount stored at a given moment.

Each column has a data type. A data type constrains the set of possible values that can be assigned to a column. For more information, see [Data types](/intl.en-US/Hologres SQL/Data types/Data types.md).

Hologres supports the following two types of tables:

-   internal table

    Internal tables store data in Hologres. The data types of internal tables must be supported by Hologres.

-   foreign table

    Foreign tables map external data sources and do not store data in Hologres. Foreign tables are read-only. Therefore, you cannot perform data manipulation language \(DML\) operations or create indexes for foreign tables.

    You can create foreign tables in Hologres to query data directly from external data sources such as MaxCompute.

    For more information about how to create a foreign table in Hologres, see [CREATE FOREIGN TABLE](/intl.en-US/Hologres SQL/DDL/FOREIGN TABLE/CREATE FOREIGN TABLE.md).


## partitioned table

Partitioned tables are parent tables that are divided into multiple segments based on partition keys. These segments are called partitions. Different types of data are stored in different partitions. In Hologres, only one partition key can be used for list partitioning. When you query data, you can specify filters based on partition key values in query statements to exclude irrelevant partitions. This improves the query speed.

For more information about how to create and delete a partitioned table in Hologres, see [DROP PARTITION TABLE](/intl.en-US/Hologres SQL/DDL/PARTITION TABLE/DROP PARTITION TABLE.md).

