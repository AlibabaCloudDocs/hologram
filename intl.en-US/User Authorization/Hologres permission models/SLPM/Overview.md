---
keyword: SLPM
---

# Overview

This topic describes the schema-level permission model \(SLPM\) in Hologres.

## Background information

Hologres is compatible with PostgreSQL and allows you to use the standard PostgreSQL authorization model. For more information, see [Standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres permission models/Standard PostgreSQL authorization model.md). In the standard PostgreSQL authorization model, permissions are managed in a fine-grained manner. To simplify operations, Hologres provides the following two simple permission models for different business scenarios:

-   Simple permission model \(SPM\): This model allows you to grant a user database-level permissions with ease. For more information, see [SPM](/intl.en-US/User Authorization/Hologres permission models/SPM/Overview.md).
-   SLPM: This model divides permissions based on schemas. Compared with the SPM, this model manages permissions in a more fine-grained manner. If you want to divide permissions in a strict manner and grant permissions with ease, we recommend that you use this model.

    **Note:** In the Hologres console, you cannot grant a user permissions by using the SLPM. You must connect a Hologres instance to a development tool and execute SQL statements to grant permissions.


## User groups and permissions in the SLPM

After you enable the SLPM, the system generates the following user groups with different permissions for each database:

-   Superuser
-   \{db\}.admin
-   \{db\}.\{schema\}.developer
-   \{db\}.\{schema\}.writer
-   \{db\}.\{schema\}.viewer

The following table describes the permissions of each user group.

|User group|Permission|
|----------|----------|
|Superuser|Serves as an administrator of a Hologres instance and has all the permissions on the instance.|
|\{db\}.admin|-   Serves as an administrator of a database.
-   Has all the permissions of the \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, and \{db\}.\{schema\}.viewer groups of the database.
-   Owns the database and has the permission to delete the database.
-   Has the permissions to manage members in the \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, and \{db\}.\{schema\}.viewer groups of the database. For example, an administrator can add members to or remove members from these groups.
-   Has the permissions to create a user and add the user to a user group.
-   Has the permissions to query, add, modify, and delete objects such as schemas in the database.
-   Has the permission to modify the configurations of the database. |
|\{db\}.\{schema\}.developer|-   Serves as a developer of a schema in a database.
-   Has all the permissions of the \{db\}.\{schema\}.writer and \{db\}.\{schema\}.viewer groups.
-   Owns all non-system objects in the schema, including tables, foreign tables, table-like objects such as views, functions, procedures, foreign servers, foreign data wrappers, types, and languages. In addition, developers can query, add, modify, and delete the tables in the schema.
-   Has the permissions to use database-level foreign servers, foreign data wrappers, types, and languages.
-   Has the USAGE and CREATE permissions on this schema and can use data definition language \(DDL\) to create tables, views, and foreign tables in the schema. |
|\{db\}.\{schema\}.writer|-   Serves as a writer of a schema in a database.
-   Has all the permissions of the \{db\}.\{schema\}.viewer group.
-   Has the permissions to execute the SELECT, INSERT, UPDATE, and DELETE statements on all tables, foreign tables, and table-like objects such as views in the schema.
-   Has the permissions to query, add, modify, and delete objects in the schema.
-   Has the permissions to access or use functions and procedures in the schema.
-   Has the permissions to use database-level foreign servers, foreign data wrappers, types, and languages.
-   Has the USAGE permission but cannot execute DDL statements on the schema. |
|\{db\}.\{schema\}.viewer|-   Serves as an analyst of a schema in a database.
-   Has the SELECT permission on all tables, foreign tables, and table-like objects such as views in the schema.
-   Has the permissions to access or use functions and procedures in the schema.
-   Has the permissions to use database-level foreign servers, foreign data wrappers, types, and languages.
-   Has the USAGE permission on the schema. |

