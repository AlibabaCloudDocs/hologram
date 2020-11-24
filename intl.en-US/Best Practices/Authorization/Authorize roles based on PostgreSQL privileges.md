---
keyword: [best practices, PostgreSQL privileges, authorize roles]
---

# Authorize roles based on PostgreSQL privileges

This topic provides the best practices for Hologres when you authorize roles based on PostgreSQL privileges. This way, you can simplify authorization and manage privileges in a fine-grained manner.

Hologres is compatible with PostgreSQL and supports authorization based on PostgreSQL privileges. Hologres also provides an authorization method called Simple Permission Model \(SPM\). For more information, see [SPM](https://help.aliyun.com/document_detail/158698.html?spm=a2c4g.11174283.6.596.2411729fVnA6oV).

However, SPM manages privileges in a coarse-grained manner. If you need to manage privileges in a fine-grained manner, see the "Best practice 1" and "Best practice 2" sections.

## Overview of PostgreSQL privileges

For information about PostgreSQL privileges, see [5.7. Privileges](https://www.postgresql.org/docs/11/sql-createuser.html).

PostgreSQL privileges have the following limits:

-   PostgreSQL privileges apply only to the existing objects and do not apply to new objects. Example:
    1.  User1 executes the `GRANT SELECT ON ALL TABLES IN SCHEMA public TO User2;` statement to authorize User2 to select all the tables in the **public** schema.
    2.  User1 creates a table named **table\_new** in the public schema.
    3.  A `Permission denied` error occurs when User2 executes the `SELECT * FROM table_new` statement.

        The SELECT privilege that User1 grants to User2 apply only to the existing tables in the **public** schema and do not apply to new tables in the **public** schema. Therefore, the preceding error occurred.

-   You can execute the `ALTER DEFAULT PRIVILEGES` statement to grant default privileges on the objects created in the future to all the roles. For more information, see [ALTER DEFAULT PRIVILEGES](https://www.postgresql.org/docs/11/sql-alterdefaultprivileges.html). The default privileges apply only to the objects created in the future. The following statement is used as an example:

    ```
    ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO PUBLIC; // By default, all the roles can read the new tables in the **public** schema.
    ```

    You can also execute the `ALTER DEFAULTPRIVILEGES FOR ROLE xxx` statement to grant default privileges on new objects to xxx. The default privileges can be granted only when the current role and xxx meet one of the following requirements:

    -   The current user is a member of the permission group to which xxx belongs.
    -   The current role is a superuser. xxx can be either a role or a permission group.
    You can use the `\ddp` command to check whether the `ALTER DEFAULT PRIVILEGES` statement takes effect. Default privileges are stored in the`pg_catalog.pg_default_acl` catalog.

    `ALTER DEFAULT PRIVILEGES` serves as a trigger. When you create a table, Hologres compares the table and the `pg_catalog.pg_default_acl` catalog based on the current role and schema information. If matches are found, the corresponding match rules are added.

    **Note:**

    -   Only the current user can be used for comparison. The permission group to which the current role belongs cannot be used for comparison.
    -   The `ALTER DEFAULT PRIVILEGES` can be executed only when you create a table. If you execute the `ALTER TABLE <TABLE> OWNER TO XX` statement after you create a table, the `ALTER DEFAULT PRIVILEGES` statement is not executed.
    Assume that User1 belongs to Group1 and you want to grant privileges to a table created in the future and compare the table and Group1. The following results are obtained:

    -   If the current role is User1, no matches are found during the comparison.
    -   If you execute the `SET SESSION ROLE Group1` statement to change the current role to Group1 before you create a table, matches are found during the comparison. Then, the privileges are automatically granted to the table.
-   Only the table owner can delete a table.

    You can decide whether the table can be deleted based on the current role. Only the following roles have the DELETE privilege:

    -   The owner of the table.
    -   The owner of the schema to which the table belongs.
    -   Superuser
-   By default, the user who creates a table is the owner of the table. The user has all the privileges on the table, including the DELETE privilege.

    The following sample statements are used to assign a table to a new owner:

    ```
    alter table <table> owner to ; // Assign the table to User2.
    alter table <table> owner to GROUP1; // Assign the table to GROUP1.
    ```

    The following limits apply when a table is assigned to a new owner:

    -   User1 is the owner of the table.
    -   User1 must directly or indirectly belong to Group1.

        For example, User1 is a member of Group1, or User1 is a member of a group in Group1.

    -   Group1 must have the USAGE privilege on the schema.
    -   A superuser can assign a table to a new owner.

## Assign privileges

You must assign the following items before you manage privileges:

-   The total number of permission groups.
-   The privileges of the permission groups.
-   The roles in each permission group.
-   The roles that can delete tables and the time that tables can be deleted.
-   The schemas to which the permission groups respectively belong.

We recommend that you perform the following operations before you manage privileges:

-   Create permission groups and assign their privileges.

    Permission groups are divided into the following types:

    -   XX\_DEV\_GROUP: the owner of a table. The owner has all the privileges on the table.
    -   XX\_WRITE\_GROUP: the privileges to write data to a table.
    -   XX\_VIEW\_GROUP: the privileges to view data in a table.
    XX indicates a project. For example, the permission groups of the PROJ1 project include PROJ1\_DEV\_GROUP, PROJ1\_WRITE\_GROUP, and PROJ1\_VIEW\_GROUP.

    **Note:** The preceding naming formats are only for reference.

-   Assign schemas for the permission groups.

    We recommend that you assign a schema for the permission groups of a project.

    Each permission group can own multiple tables. However, each table can belong to only one permission group. For example, TABLE1 can belong only to PROJ1\_DEV\_GROUP.

    Each role can belong to multiple permission groups. For example, USER1 can belong to PROJ1\_DEV\_GROUP and PROJ2\_DEV\_GROUP.


## Best practice 1

A table is used as an example.

All the members in the permission group where the owner belongs can manage or delete the table.

Perform the following operations to add a role to the permission group where the owner belongs:

1.  Create permission groups.

    You can create permission groups based on your needs. Assume that the project is named PROJ1. The following statements are used as an example:

    ```
    create role PROJ1_DEV_GROUP; // The owner of the table. The owner has all the privileges on the table.
    create role PROJ1_WRITE_GROUP; // The privileges to write data to the table.
    create role PROJ1_VIEW_GROUP; // The privileges to view data in the table.
    ```

2.  Grant privileges to the schemas.

    You must grant privileges to the schemas to which the permission groups belong. Assume that PROJ1 belongs to schema1. The following statements are used as an example:

    ```
    Grant all the privileges of schema1 to PROJ1.
    grant create,usage on schema SCHEMA1 to PROJ1_DEV_GROUP;
    grant usage on schema SCHEMA1 to PROJ1_WRITE_GROUP;
    grant usage on schema SCHEMA1 to PROJ1_VIEW_GROUP;
    ```

    **Note:**

    -   Each project can belong to multiple schemas. Each schema can have multiple projects.
    -   By default, all the roles in the **public** schema have the CREATE privilege and the USAGE privilege.
3.  Create roles and manage the permission groups.

    After you grant privileges to the permission groups as a superuser, you must create and add a role to the permission groups. The following statements are used as an example:

    ```
    create user "USER1";
    grant PROJ1_DEV_GROUP to "USER1"; 
    
    create user "USER2";
    grant PROJ1_VIEW_GROUP to "USER2";
    ```

4.  Create a table and grant the privileges on the table to the roles.

    When a table is created, the owner or a superuser must grant the privileges on the table to the roles. Take note that the owner must be a member of PROJ1\_DEVE\_GROUP. The following statements are used as an example:

    ```
    grant all on table SCHEMA1.TABLE1 to PROJ1_WRITE_GROUP; // Grant PROJ1_WRITE_GROUP the privileges to write data to table1.
    grant select on table SCHEMA1.TABLE1 to PROJ1_VIEW_GROUP; // Grant PROJ1 _VIEW_GROUP the SELECT privilege.
    alter table SCHEMA1.TABLE1 owner to PROJ1_DEV_GROUP; // Assign TABLE1 to PROJ1_DEV_GROUP.
    ```


## Best practice 2

The `ALTER DEFAULT PRIVILEGES` statement is used in this example.

Perform the following operations to assign a table to a new owner or change the project to which a role belongs:

1.  Create permission groups.

    You can create permission groups based on your needs. Assume that the project is named PROJ1. The following statements are used as an example:

    ```
    create role PROJ1_DEV_GROUP; // The owner of the table. The owner has all the privileges on the table.
    create role PROJ1_WRITE_GROUP; // The privileges to write data to the table.
    create role PROJ1_VIEW_GROUP; // The privileges to view data in the table.
    ```

2.  Grant privileges to the schemas.

    You must grant privileges to the schemas to which the permission groups belong. Assume that PROJ1 belongs to schema1. The following statements are used as an example:

    ```
    Grant all the privileges of schema1 to PROJ1.
    grant create,usage on schema SCHEMA1 to PROJ1_DEV_GROUP;
    grant usage on schema SCHEMA1 to PROJ1_WRITE_GROUP;
    grant usage on schema SCHEMA1 to PROJ1_VIEW_GROUP;
    ```

    **Note:**

    -   Each project can belong to multiple schemas. Each schema can have multiple projects.
    -   By default, all the roles in the **public** schema have the CREATE privilege and the USAGE privilege.
3.  Create roles and grant default privileges to the roles.

    After privileges are granted to the schemas, a superuser needs to create roles and add the roles to the permission groups. The superuser also needs to grant default privileges to the roles.

    The following statements are used as an example:

    ```
    create user "USER1";
    
    alter default privileges for role "USER1" grant all on tables to PROJ1_DEV_GROUP; // Grant PROJ1_DEV_GROUP the default privileges on the table created by USER1.
    alter default privileges for role "USER1" grant all on tables to PROJ1_WRITE_GROUP; // Grant PROJ1_WRITE_GROUP the default privileges on the table created by USER1.
    alter default privileges for role "USER1" grant select on tables to PROJ1_VIEW_GROUP; // Grant PROJ1_VIEW_GROUP the default privileges on the table created by USER1.
    
    grant PROJ1_DEV_GROUP to "USER1"; // Add USER1 to PROJ1_DEV_GROUP.
    ```

4.  Assign the table to a new owner.

    If you want to authorize other members of PROJ1\_DEV\_GROUP to perform operations on the table, you can assign the table to PROJ1\_DEV\_GROUP.

    The ALTER TABLE <TABLE\> OWNER TO PROJ1\_DEV\_GROUP statement must be executed by a superuser. Assume that the table is named TABLE1. The following statement is used as an example:

    ```
    alter table SCHEMA1.TABLE1 owner to PROJ1_DEV_GROUP; // Assign TABLE1 to PROJ1_DEV_GROUP.
    ```

    A table can be assigned to a new owner when the following requirements are met:

    -   The table is newly created and a superuser modifies the owner on a regular basis.
    -   The table is assigned to a new owner before operations are performed on it.
    -   If the table is modified or deleted by the owner or a superuser, you do not need to execute the preceding statements.
5.  Change the default project to which a role belongs.

    Only the owner or the role can execute the `ALTER DEFAULT PRIVILEGE` statement to revoke the default privileges granted to the current project. Then the owner or the role execute the `ALTER DEFAULT PRIVILEGE` statement again to grant default privileges to another project.

    If the project is changed, the table is not affected. The following statements are used as an example:

    ```
    Disable the default privileges granted to the current project.
    alter default privileges for role "USER1" revoke all on tables from PROJ1_DEV_GROUP;
    alter default privileges for role "USER1" revoke all on tables from PROJ1_WRITE_GROUP;
    alter default privileges for role "USER1" revoke select on tables from PROJ1_VIEW_GROUP;
    
    Grant default privileges to another project.
    alter default privileges for role "USER1" grant all on tables to PROJ2_DEV_GROUP;
    alter default privileges for role "USER1" grant all on tables to PROJ2_WRITE_GROUP;
    alter default privileges for role "USER1" grant select on tables to PROJ2_VIEW_GROUP;
    ```


