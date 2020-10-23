---
keyword: [Hologres, SPM, function calls]
---

# Functions of the SPM

This topic describes the functions of the simple permission model \(SPM\) in Hologres. You can call these functions to manage the SPM.

## spm\_enable

-   Description

    `spm_enable`: enables the SPM.

    After you call the `spm_enable` function to enable the SPM for a database, the system creates four user groups for the database, that is, <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer.

-   Syntax

    ```
    CALL spm_enable ();
    ```

    **Note:** To call the `spm_enable` function, you must be a superuser of the Hologres instance.

-   Notes

    After you call the `spm_enable` function to enable the SPM for a database, the following changes occur to the permissions of users:

    -   All permissions on the database are revoked from the PUBLIC user group. This prevents unauthorized users from connecting to the database.
    -   All permissions on all schemas of the database are revoked from the PUBLIC user group.
    -   Users that are added to the <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups are granted the permission to connect to the database.
    -   Users that are added to the <db\>\_admin group become the owners of the database and all the schemas of the database.
    -   Users that are added to the <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups are granted the USAGE permission on all the schemas of the database. Users that are added to the <db\>\_developer group are also granted the CREATE permission on all the schemas of the database.
    -   Users that are added to the <db\>\_admin and <db\>\_developer groups have the permission to create objects. For these objects, users that are added to the <db\>\_developer group are the owners, users that are added to the <db\>\_writer group have the read and write permissions, and users that are added to the <db\>\_viewer group have the read-only permission.

## spm\_migrate

-   Description

    `spm_migrate`: transfers the ownership of existing objects in a database to the developers that are specified in the SPM.

-   Syntax

    ```
    CALL spm_migrate ( [ batch_size ] );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|Value range|
    |---------|-----------|-----------|
    |batch\_size|The number of objects whose ownership is transferred at a time.If you set this parameter to 0, the system transfers the ownership for objects of no more than the value of the [max\_locks\_per\_transaction](https://www.postgresql.org/docs/9.1/runtime-config-locks.html) parameter at a time.

|The value of the batch\_size parameter must fall into the closed interval `[0, max_locks_per_transaction]`. Otherwise, this parameter is invalid.If the number of existing objects in the database exceeds the value of the max\_locks\_per\_transaction parameter, you must repeat calling the `spm_migrate` function until the ownership of all the objects is transferred.

In addition, we recommend that you [submit a ticket](https://workorder-intl.console.aliyun.com/) to increase the value of the max\_locks\_per\_transaction parameter before you call the spm\_migrate function. |

-   Notes
    -   After you call the `spm_migrate` function in a database, if the `DONE BUT NOT COMPLETED` message is returned, the ownership of some objects remains not transferred. You must call the `spm_migrate` function again.
    -   When the `COMPLETED` message is returned, the ownership of all the objects is transferred.
-   Examples

    ```
    CALL spm_migrate (); // Transfer the ownership for objects of no more than the value of the max_locks_per_transaction parameter at a time to the developers that are specified in the SPM.
    
    CALL spm_migrate (128); // Transfer the ownership of 128 objects at a time.
    ```


## spm\_create\_user

-   Description

    `spm_create_user`: creates a user in a Hologres instance. After you call this function to create a user in a Hologres instance, the user only has the permission to connect to the instance. To manage databases in the instance, the user must be granted the required permissions.

    **Note:** To call the spm\_create\_user function, you must be a superuser or a member of the <db\>\_admin group of the database.

-   Syntax

    ```
    CALL spm_create_user ( user_name [, role_name] );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|
    |---------|-----------|
    |user\_name|The name of the user to be created. You can specify the name in one of the following formats:    -   The ID or display name of an Alibaba Cloud account, such as 13432193xxxx or xx@aliyun.com.
    -   The UID of a RAM user prefixed with p4\_ or the display name of the RAM user, such as p4\_202338382183xxx or RAM$mainaccount:subuser. |
    |role\_name|The group to which the user is to be added. You can add the user to one of the following groups: <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer.|

-   Examples

    ```
    CALL spm_create_user ('my_test@aliyun.com');
    CALL spm_create_user ('RAM$my_test:mysubuser', 'mydb_developer');
    CALL spm_create_user ('13532313103042xxx');
    CALL spm_create_user ('p4_23319103042xxx', 'mydb_admin');
    ```


## spm\_grant

-   Description

    `spm_grant`: adds a user to one of the following user groups of a database: <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer.

    **Note:** To call the spm\_grant function, you must be a superuser or a member of the <db\>\_admin group of the database.

-   Syntax

    ```
    CALL spm_grant ( role_name, user_name );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|
    |---------|-----------|
    |user\_name|The name of the user to be added to the specified group. You can specify the name in one of the following formats:    -   The ID or display name of an Alibaba Cloud account, such as 13432193xxxx or xx@aliyun.com.
    -   The UID of a RAM user prefixed with p4\_ or the display name of the RAM user, such as p4\_202338382183xxx or RAM$mainaccount:subuser. |
    |role\_name|The group to which the user is to be added. You can add the user to one of the following groups: <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer.|

-   Notes
    -   You can call the spm\_grant function only in a database with the SPM enabled.
    -   The value of the user\_name parameter must be the account ID or display name of an Alibaba Cloud account or a RAM user.
    -   Valid values of the role\_name parameter are <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer. You are not allowed to call this function to add a user to other groups.
-   Examples

    ```
    CALL spm_grant ('mydb_developer', 'p4_202338382183xxx'); // Add the specified RAM user to the <mydb>_developer group.
    CALL spm_grant ('mydb_admin', 'RAM$my_test:xxx'); // Add the specified RAM user to the <mydb>_admin group.
    CALL spm_grant ('otherdb_admin', '13532313103042xxx'); // This function fails because you can add a user only to a group of the current database. To add a user to a group of another database, connect to the database and call the spm_grant function.
    ```


## spm\_revoke

-   Description

    `spm_revoke`: removes a user from the <db\>\_admin, <db\>\_developer, <db\>\_writer, or <db\>\_viewer group of a database.

    **Note:** To call the spm\_revoke function, you must be a superuser or a member of the <db\>\_admin group of the database.

-   Syntax

    ```
    CALL spm_revoke ( role_name, user_name );
    ```

-   Notes
    -   You can call the spm\_revoke function only in a database with the SPM enabled.
    -   The value of the user\_name parameter must be the account ID or display name of an Alibaba Cloud account or a RAM user.
    -   Valid values of the role\_name parameter are <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer. You are not allowed to call this function to remove a user from other groups.
-   Examples

    ```
     CALL spm_revoke ('mydb_developer', 'p4_202338382183xxx'); // Remove the specified RAM user from the <mydb>_developer group.
    CALL spm_revoke ('mydb_admin', 'RAM$my_test:xxx'); // Remove the specified RAM user from the <mydb>_admin group.
    CALL spm_revoke ('otherdb_admin', '13532313103042xxx'); // This function fails because you can remove a user only from a group of the current database. To remove a user from a group of another database, connect to the database and call the spm_revoke function.
    ```


## spm\_disable

-   Description

    `spm_disable`: disables the SPM for a database.

    **Note:** To call the spm\_disable function, you must be a superuser.

-   Syntax

    ```
    CALL spm_disable ();
    ```

-   Notes

    After you disable the SPM for a database, the following changes occur to the permissions of user groups of the database:

    -   The <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups are reserved. You can execute authorization statements in the standard PostgreSQL authorization model to add users to these user groups. Members in the <db\>\_developer group are still the owners of the database.
    -   The CONNECT and TEMPORARY permissions on the database are granted to all users.
    -   The USAGE and CREATE permissions on the public schema of the database are granted to all users.
    -   The EXECUTE permission on the functions and procedures of the public schema in the database is granted to all users.
    -   The USAGE permission on the user-defined languages of the database is granted to all users.
    -   The USAGE permission on the user-defined types of the database is granted to all users.
-   Examples

    ```
    CALL spm_disable ();
    ```


## spm\_cleanup

-   Description

    `spm_cleanup`: deletes the <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups of a database after the SPM is disabled.

    **Note:** To call the spm\_cleanup function, you must be a superuser.

-   Syntax

    ```
    CALL spm_cleanup ( db_name [, batch_size ] );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|Value range|
    |---------|-----------|-----------|
    |db\_name|The name of the database for which you want to delete user groups. If the database name contains special characters or uppercase letters, enclose it in double quotation marks \(" "\), such as `"MYDB"`.|None|
    |batch\_size|The number of objects whose ownership is transferred at a time. When you delete user groups of a database, the ownership of objects in the database is transferred.If you set this parameter to 0, the system transfers the ownership for objects of no more than the value of the [max\_locks\_per\_transaction](https://www.postgresql.org/docs/9.1/runtime-config-locks.html) parameter at a time.

|The value of the batch\_size parameter must fall into the closed interval `[0, max_locks_per_transaction]`. Otherwise, this parameter is invalid.If the number of existing objects in the database exceeds the value of the max\_locks\_per\_transaction parameter, you must repeat calling the `spm_migrate` function until the ownership of all the objects is transferred.

In addition, we recommend that you [submit a ticket](https://workorder-intl.console.aliyun.com/) to increase the value of the max\_locks\_per\_transaction parameter before you call the spm\_cleanup function. |

-   Notes
    -   After you call the spm\_cleanup function in a database, if the `DONE BUT NOT COMPLETED` message is returned, the ownership of some objects remains not transferred. You must call the `spm_cleanup` function again.
    -   When the `COMPLETED` message is returned, the ownership of all the objects is transferred.
-   Examples

    ```
    CALL spm_cleanup ('mydb'); // Delete all user groups of the specified database and transfer the ownership for objects of no more than the value of the max_locks_per_transaction parameter at a time to the current user who calls this function.
    CALL spm_cleanup ('mydb', 128); // Delete all user groups of the specified database and transfer the ownership of 128 objects at a time to the current user who calls this function.
    ```

    -   Case 1: Delete a database and then user groups of the database.

        ```
        drop database mydb;
        CALL spm_cleanup ('mydb'); // You can delete user groups of a deleted database at a time.
        ```

    -   Case 2: Delete user groups of an existing database. In this case, you must connect to the database. Otherwise, an error message is returned.

        ```
         CALL spm_cleanup ('otherdb');
        ERROR:  Permission Denied. execute in database otherdb, or drop database before call spm_cleanup.
        ```


## spm\_alter\_database\_rename

-   Description

    `spm_alter_database_rename`: renames a database.

    After you rename a database with the SPM enabled, the names of the <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups are automatically modified. You must call the `spm_alter_database_rename` function to rename a database when the SPM is enabled.

    **Note:** To call the spm\_alter\_database\_rename function, you must be a superuser or a member of the <db\>\_admin group of the database.

-   Syntax

    ```
    CALL spm_alter_database_rename ( old_name, new_name );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|
    |---------|-----------|
    |old\_name|The original name of the database to be renamed. If the name contains special characters or uppercase letters, enclose it in double quotation marks \(" "\), such as `"MYDB"`.|
    |new\_name|The new name that you want to specify for the database. If the name contains special characters or uppercase letters, enclose it in double quotation marks \(" "\), such as `"MYDB"`.|

-   Examples

    ```
    CALL spm_alter_database_rename ('olddb', 'newdb');  
    ```


## Permissions on calling SPM functions

The following table describes the permissions on calling SPM functions that are granted to different user groups.

|Function|Description|Superuser|db\_admin|db\_developer \| db\_writer \| db\_viewer|
|--------|-----------|---------|---------|-----------------------------------------|
|`spm_enable`|Enables the SPM.|Granted|Not granted|Not granted|
|`spm_disable`|Disables the SPM.|Granted|Not granted|Not granted|
|`spm_grant`|Adds a user to a user group.|Granted|Granted|Not granted|
|`spm_revoke`|Removes a user from a user group.|Granted|Granted|Not granted|
|`spm_alter_database_rename`|Renames a database. After you call this function in a database, the schemas and user groups of the database are automatically modified.|Granted|Granted|Not granted|
|`spm_migrate`|Transfers the ownership of all tables and table-like objects in a database to the specified developers after the SPM is enabled for the database.|Granted|Granted|Not granted|
|`spm_cleanup`|Deletes all user groups of a database.|Granted|Not granted|Not granted|
|`spm_create_user`|Creates a user in a Hologres instance and grants the user only the permission to connect to the instance.|Granted|Granted|Not granted|

## Restricted commands in the SPM

The SPM limits the usage of some commands related to permissions, as described in the following table.

|Command|Description|
|-------|-----------|
|`alter table owner to xx`|After you enable the SPM for a database, the ownership of all tables under schemas of the database is automatically transferred to the members of the <db\>\_developer group. You are not allowed to transfer the ownership of these tables.|
|`grant`|You can call the `spm_grant` function to add a user to a user group. After that, you no longer need to execute the GRANT statement.|
|`revoke`|You can call the `spm_revoke` function to remove a user from a user group. After that, you no longer need to execute the REVOKE statement.|
|`alter default privileges`|In the standard PostgreSQL authorization model, you can grant users the permissions only on existing tables and objects. To enable users to manage new tables and objects, you must grant them permissions again. In the SPM, you can add users to user groups to grant them the required permissions on all tables or objects of the specified types, including the existing ones and new ones.|
|`create role/drop role/alter role/alter role set`|You are not allowed to run these commands to manage the <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups. These user groups are generated by the system when the SPM is enabled and cannot be created or modified by users, including superusers.|
|`rename to/from`|You are not allowed to run these commands to rename the `<db>_admin, <db>_developer, <db>_writer, and <db>_viewer` groups. These user groups are generated by the system when the SPM is enabled.|
|`rename database`|You are not allowed to run this command to rename a database with the SPM enabled. Instead, you can call the `spm_alter_database_rename` function to rename the database.|

