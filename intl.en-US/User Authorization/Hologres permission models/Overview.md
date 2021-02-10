---
keyword: [Hologres, permission model]
---

# Overview

After a user logs on to a Hologres instance, the user must be granted the required permissions to perform operations on the instance. This topic describes specific development permissions that are required to allow a user to perform data analytics operations on a Hologres instance.

## Hologres authentication process

The following figure shows a complete Hologres authentication process from the step of adding a user to a Hologres instance to the step of performing operations on the instance.

![Hologres authentication process](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2739392161/p207367.png)

## Users

When you connect a user to a Hologres instance, you must execute the `create user "xxx"` statement to assign the user a role to manage the Hologres instance. Otherwise, the error message `role "xxx" does not exist` is returned. After the statement is successfully executed, the user is granted permissions on the Hologres instance. When you create or delete a Hologres user, the user is added to or deleted from a Hologres instance. To perform specific operations such as an operation for creating a table in a Hologres instance, a user must be granted permissions on a database. After the user is granted permissions on the database, the user can perform operations on only objects in this database rather than objects in other databases.

|Role|Description|
|----|-----------|
|Superuser|By default, the system sets the Alibaba Cloud account used to purchase a Hologres instance as a superuser that has full permissions on the instance. The superuser can create and delete databases, create roles, and grant permissions to roles.|
|Normal user|Users except superusers are all normal user. A normal user can access a Hologres instance and analyze data only after a superuser grants the permissions to the normal user. A normal user can also be assigned the superuser role to obtain full permissions on a Hologres instance.|
|User group|You can add zero or multiple users to a user group to facilitate user management. A user group represent a role, for example, the administrator role and developer role. Users in a user group have the same permissions. For more information about how to set a user group and grant permissions to the user group, see [PostgreSQL database roles](https://www.postgresql.org/docs/11/user-manag.html).|

## Permission models

Compatible with PostgreSQL 11, Hologres provides the standard PostgreSQL authorization model to grant a user the development permissions on a Hologres instance. In this model, the permissions are managed in a fine-grained manner and PostgreSQL authorization statements are complicated. To simplify operations, Hologres provides the simple permission model \(SPM\) that allows you to manage permissions with ease.

The following describes the SPM, the schema-level permission model \(SLPM\), and the standard PostgreSQL authorization model, and their usage scenarios.

|Permission model type|Scenario|Description|
|---------------------|--------|-----------|
|Standard PostgreSQL authorization model|This model is applicable to scenarios in which permissions are strictly managed. For example, this model allows you to grant a user permissions to use a specific table.|In this model, permissions are granted in a fine-grained and flexible manner. This way, a user can be granted permissions to use a specific table. However, the PostgreSQL authorization statements are complicated. For more information about how to grant permissions by using the standard PostgreSQL authorization model, see [Standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres permission models/Standard PostgreSQL authorization model.md).|
|SPM|This model allows you to manage database-level permissions and is applicable to scenarios in which permissions are managed in a coarse-grained manner.|In this model, specific permissions of each user group on databases cannot be changed. This model is applicable to most scenarios and the authorization operations are simple. For more information about how to grant permissions by using the SPM, see [Use the SPM](/intl.en-US/User Authorization/Hologres permission models/SPM/Use the SPM.md).|
|SLPM|This model allows you to manage schema-level permissions and is applicable to scenarios in which permissions are managed in a fine-grained manner and a simple authorization process is required.|In this model, specific permissions of each user group on schemas cannot be changed. This model is applicable to scenarios in which permissions are managed in a fine-grained manner. The authorization operations are simple. For more information about how to grant permissions by using the SLPM, see [t2021883.md\#]().|

## Permission authorization

If a user needs to perform data analytics operations on a Hologres instance, the user must be granted the specific permissions. You can select a permission model and grant the user the required permissions based on operations that the user wants to perform. The following two tables describe specific operations that can be performed and related permissions that must be granted to users by using the standard PostgreSQL authorization model, the SPM, or the SLPM.

**Note:** By default, a superuser has all permissions described in the following tables.

|Operation to be authorized by using the standard PostgreSQL authorization model|Required permission|Authorization statement|
|-------------------------------------------------------------------------------|-------------------|-----------------------|
|CREATE USER\(ROLE\)

DROP USER\(ROLE\)

|CREATEROLE|You can execute the following statement to grant User A the permission to create a role:```
alter user A createrole;
``` |
|CREATE TABLE

VIEW TABLE

FOREIGN TABLE

|The CONNECT permission on a database and the CREATE permission on a schema|You can execute the following statement to grant User A the permission to create a table under a schema:```
GRANT CREATE ON SCHEMA xx TO A;
```

By default, all users have the permission to create a table under the public schema.|
|SELECT|The USAGE or SELECT permission on a schema|You can execute one of the following statements to perform authorization operations:```
GRANT USAGE ON SCHEMA xx to A;
GRANT SELECT ON TABLE xx TO A;
GRANT SELECT ON ALL TABLES IN SCHEMA xx TO A;
``` |
|INSERT

UPDATE

DELETE

TRUNCATE

|The USAGE, INSERT, UPDATE, DELETE, or TRUNCATE permission on a schema|You can execute one of the following statements to perform authorization operations:```
GRANT USAGE ON SCHEMA xx to A;
GRANT [INSERT/UPDATE/DELETE/TRUNCATE] ON TABLE xx TO A;
GRANT [INSERT/UPDATE/DELETE/TRUNCATE] ON ALL TABLES IN SCHEMA xx TO A;
``` |
|ALTER TABLE|The ownership of a table \(You can execute an ALTER OWNER statement to change the ownership of a table.\)|You cannot execute a GRANT statement to allow User A to delete a table. Instead, you must execute the `ALTER TABLE xx OWNER TO A;` statement to transfer the ownership of the table to User A. Then, User A can delete the table.|
|DROP TABLE|
|CREATE DATABASE|CREATEDB|You can execute the following statement to grant User A the permission to create a database:```
ALTER USER A CREATEDB;
``` |
|DROP DATABASE|DB owner|You cannot execute a GRANT statement to allow User A to delete a database. Instead, you must execute the `ALTER DATABASE xx OWNER TO A;` statement to transfer the ownership of the database to User A. Then, User A can delete the database.|
|CREATE EXTENSION|DB owner|-|
|GRANT

REVOKE

|The required permissions and the GRANT OPTION permission|You can execute the following statement to grant User A a specific permission and allow User A to grant the permission to other users:```
GRANT [Privilege] TO A WITH GRANT OPTION;
``` |

|Authorization based on permission models|SPM|SLPM|
|----------------------------------------|---|----|
|Operation|Required permission|Authorization statement|Required permission|Authorization statement|
|CREATE USER\(ROLE\)

DROP USER\(ROLE\)

|DB admin|You can execute one of the following statements to grant User A the permissions that belong to the <db\>\_admin group:-   ```
call spm_grant('A', '<dbname>_admin');
```

-   ```
call spm_create_user('A', '<dbname>_admin');
```


|DB admin|You can execute one of the following statements to grant User A the permissions that belong to the \{db\}.admin group:-   ```
call slpm_create_user('A', '<dbname>.<schema>.admin');
```

-   ```
call slpm_grant('A', '<dbname>.<schema>.admin');
``` |
|CREATE TABLE

VIEW TABLE

FOREIGN TABLE

|The permissions that belong to the superuser, <db\>\_admin, or <db\>\_developer group|You can grant a user the permissions that belong to the <db\>\_admin or <db\>\_developer group by using the SPM authorization statements described in this table.|The permissions that belong to the superuser, \{db\}.admin, or \{db\}.\{schema\}.developer group|You can grant a user the permissions that belong to the \{db\}.admin or \{db\}.\{schema\}.developer group by using the SLPM authorization statements described in this table.|
|SELECT|The permissions that belong to the superuser, <db\>\_admin, <db\>\_developer, <db\>\_writer, or <db\>\_viewer group|You can grant a user the permissions that belong to the <db\>\_admin, <db\>\_developer, <db\>\_writer, or <db\>\_viewer group by using the SPM authorization statements described in this table.|The permissions that belong to the superuser, \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, or \{db\}.\{schema\}.viewer group|You can grant a user the permissions that belong to the \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, or \{db\}.\{schema\}.viewer group by using the SLPM authorization statements described in this table.|
|INSERT

UPDATE

DELETE

TRUNCATE

|The permissions that belong to the superuser, <db\>\_developer, <db\>\_writer, or <db\>\_viewer group|You can grant a user the permissions that belong to the <db\>\_admin, <db\>\_developer, or <db\>\_writer group by using the SPM authorization statements described in this table.|The permissions that belong to the superuser, \{db\}.admin, \{db\}.\{schema\}.developer, or \{db\}.\{schema\}.writer group|You can grant a user the permissions that belong to the \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, or \{db\}.\{schema\}.viewer group by using the SLPM authorization statements described in this table.|
|ALTER TABLE|The permissions that belong to the superuser, <db\>\_admin, or <db\>\_developer group|You can grant a user the permissions that belong to the <db\>\_admin or <db\>\_developer group by using the SPM authorization statements described in this table.|The permissions that belong to the superuser, \{db\}.admin, or \{db\}.\{schema\}.developer group|You can grant a user the permissions that belong to the \{db\}.admin or \{db\}.\{schema\}.developer group by using the SLPM authorization statements described in this table.|
|DROP TABLE|
|CREATE DATABASE

DROP DATABASE

CREATE EXTENSION

|DB admin|You can grant a user the permissions that belong to the <db\>\_admin group by using the SPM authorization statements described in this table.|DB admin|You can grant a user the permissions that belong to the \{db\}.admin group by using the SLPM authorization statements described in this table.|
|GRANT

REVOKE

|DB admin|You can execute following statements to grant User A the GRANT and REVOKE permissions:```
call spm_grant('A', 'role');
call spm_revoke('A', 'role');
```

|DB admin|You can execute following statements to grant User A the GRANT and REVOKE permissions:```
call slpm_grant('A', 'role');
call slpm_revoke('A', 'role');
``` |

