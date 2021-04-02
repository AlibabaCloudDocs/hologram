---
keyword: [Hologres, SPM, function calls]
---

# Functions of the SPM

This topic describes the functions of the simple permission model \(SPM\) in Hologres. You can call these functions to manage the SPM.

## Overview

The SPM supports the following functions:

-   [spm\_enable](#section_3jy_fy7_o1f): enables the SPM.
-   [spm\_migrate](#section_kp0_48p_ykm): transfers the ownership of existing objects such as tables, views, and foreign tables in a database to the developers that are specified in the SPM.
-   [spm\_create\_user](#section_6gf_zbc_oon): creates a user who has only the permission to connect to a Hologres instance. To manage databases in the instance, the user must be granted the required permissions.
-   [spm\_grant](#section_tll_nnm_dj1): adds a user to a user group.
-   [spm\_revoke](#section_ypq_e9k_v8i): removes a user from a user group.
-   [spm\_disable](#section_13d_pfx_0c1): disables the SPM for a database.
-   [spm\_cleanup](#section_31l_h1r_bfb): deletes user groups of a database after the SPM is disabled.
-   [spm\_alter\_database\_rename](#section_nyp_098_9ry): renames a database.

## spm\_enable

-   Description

    spm\_enable: enables the SPM.

    After you call the spm\_enable function to enable the SPM for a database, the system creates the <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups for the database.

-   Syntax

    ```
    CALL spm_enable ();
    ```

    **Note:** To call the spm\_enable function, you must be a superuser of the Hologres instance.

-   Usage notes

    After you call the spm\_enable function to enable the SPM for a database, the following changes occur to the permissions of users:

    -   All permissions on the database are revoked from the PUBLIC group. This prevents unauthorized users from connecting to the database.
    -   All permissions on all schemas of the database are revoked from the PUBLIC group.
    -   Users that are added to the <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups are granted permissions to connect to the database.
    -   Users that are added to the <db\>\_admin group become the owners of the database and all the schemas of the database.
    -   Users that are added to the <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups are granted the USAGE permission on all the schemas of the database. Users that are added to the <db\>\_developer group are also granted the CREATE permission on all the schemas of the database.
    -   Users that are added to the <db\>\_admin and <db\>\_developer groups have the permission to create objects. For these objects, users that are added to the <db\>\_developer group are the owners, users that are added to the <db\>\_writer group have the read and write permissions, and users that are added to the <db\>\_viewer group have the read-only permission.

## spm\_migrate

-   Description

    spm\_migrate: transfers the ownership of existing objects such as tables, views, and foreign tables in a database to the developers that are specified in the SPM.

-   Syntax

    ```
    CALL spm_migrate ( [ batch_size ] );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|Value range|
    |---------|-----------|-----------|
    |batch\_size|The maximum number of objects whose ownership can be transferred at a time.If you set this parameter to 0, the maximum number of objects whose ownership can be transferred at a time is specified by the [max\_locks\_per\_transaction](https://www.postgresql.org/docs/9.1/runtime-config-locks.html) parameter.

|The value of the batch\_size parameter must fall into the closed interval \[0, max\_locks\_per\_transaction\]. Otherwise, this parameter is invalid. The maximum value of the `max_locks_per_transaction` parameter is 64.If the number of existing objects in the database exceeds the value of the batch\_size parameter, you must repeat calling the spm\_migrate function until the ownership of all the objects is transferred.

In addition, we recommend that you [submit a ticket](https://workorder-intl.console.aliyun.com/) to increase the value of the max\_locks\_per\_transaction parameter before you call the spm\_migrate function. |

-   Usage notes
    -   After you call the spm\_migrate function in a database, if the `DONE BUT NOT COMPLETED` message is returned, the ownership of part of objects is not transferred. You must call the spm\_migrate function again.
    -   When the `COMPLETED` message is returned, the ownership of all the objects is transferred.
-   Examples

    ```
    CALL spm_migrate (); // Transfer the ownership for objects of no more than the value of the max_locks_per_transaction parameter at a time to the developers that are specified in the SPM.
    
    CALL spm_migrate (128); // Transfer the ownership of 128 objects at a time.
    ```


## spm\_create\_user

-   Description

    spm\_create\_user: creates a user in a Hologres instance. After you call this function to create a user in a Hologres instance, the user has only the permission to connect to the instance. To manage databases in the instance, the user must be granted the required permissions.

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
    |role\_name|The group to which the user is to be added. You can add the user to one of the following groups based on your business needs:    -   \{db\}\_admin
    -   \{db\}\_developer
    -   \{db\}\_writer
    -   \{db\}\_viewer |

-   Examples

    ```
    CALL spm_create_user ('my_test@aliyun.com');
    CALL spm_create_user ('RAM$my_test:mysubuser', 'mydb_developer');
    CALL spm_create_user ('13532313103042xxx');
    CALL spm_create_user ('p4_23319103042xxx', 'mydb_admin');
    ```


## spm\_grant

-   Description

    spm\_grant: adds a user to one of the following user groups of a database: <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer.

    **Note:** To call the spm\_grant function, you must be a superuser or a member of the <db\>\_admin group of the database.

-   Syntax

    ```
    CALL spm_grant ( role_name, user_name );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|
    |---------|-----------|
    |role\_name|The group to which the user is to be added. You can add the user to one of the following groups based on your business needs:    -   \{db\}\_admin
    -   \{db\}\_developer
    -   \{db\}\_writer
    -   \{db\}\_viewer
For more information about the permissions of user groups, see [Overview](/intl.en-US/User Authorization/Hologres permission models/SPM/Overview.md).|
    |user\_name|The name of the user to be added to the specified user group. You can specify the name in one of the following formats:    -   The ID or display name of an Alibaba Cloud account, such as 13432193xxxx or xx@aliyun.com.
    -   The UID of a RAM user prefixed with p4\_ or the display name of the RAM user, such as p4\_202338382183xxx or RAM$mainaccount:subuser. |

-   Usage notes
    -   You can call the spm\_grant function only in a database for which the SPM is enabled.
    -   The value of the user\_name parameter must be the account ID or display name of an Alibaba Cloud account or a RAM user.
    -   The value of the role\_name parameter must be in one of the following formats: <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer.
-   Examples

    ```
    CALL spm_grant ('mydb_developer', 'p4_202338382183xxx'); // Add the specified RAM user to the mydb_developer group.
    CALL spm_grant ('mydb_admin', 'RAM$my_test:xxx'); // Add the specified RAM user to the mydb_admin group.
    CALL spm_grant ('otherdb_admin', '13532313103042xxx'); // This function fails because you can add a user only to a group of the current database. To add a user to a group of another database, connect to the database and call the spm_grant function.
    ```


## spm\_revoke

-   Description

    spm\_revoke: removes a user from the <db\>\_admin, <db\>\_developer, <db\>\_writer, or <db\>\_viewer group of a database.

    **Note:** To call the spm\_revoke function, you must be a superuser or a member of the <db\>\_admin group of the database.

-   Syntax

    ```
    CALL spm_revoke ( role_name, user_name );
    ```

-   Usage notes
    -   You can call the spm\_revoke function only in a database for which the SPM is enabled.
    -   The value of the user\_name parameter must be the account ID or display name of an Alibaba Cloud account or a RAM user.
    -   The value of the role\_name parameter must be in one of the following formats: <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer.
-   Examples

    ```
     CALL spm_revoke ('mydb_developer', 'p4_202338382183xxx'); // Remove the specified RAM user from the mydb_developer group.
    CALL spm_revoke ('mydb_admin', 'RAM$my_test:xxx'); // Remove the specified RAM user from the mydb_admin group.
    CALL spm_revoke ('otherdb_admin', '13532313103042xxx'); // This function fails because you can remove a user only from a group of the current database. To remove a user from a group of another database, connect to the database and call the spm_revoke function.
    ```


## spm\_disable

-   Description

    spm\_disable: disables the SPM for a database.

    **Note:** To call the spm\_disable function, you must be a superuser.

-   Syntax

    ```
    CALL spm_disable ();
    ```

-   Usage notes

    After you disable the SPM for a database, the following changes occur to the permissions of user groups of the database:

    -   The <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups are retained. You can execute authorization statements in the standard PostgreSQL authorization model to add users to these user groups. Members in the <db\>\_developer group are still the owners of the database.
    -   The CONNECT and TEMPORARY permissions on the database are granted to the PUBLIC group.
    -   The USAGE and CREATE permissions on the public schema of the database are granted to the PUBLIC group.
    -   The EXECUTE permission on the functions and procedures of the public schema in the database is granted to the PUBLIC group.
    -   The USAGE permission on the user-defined languages of the database is granted to the PUBLIC group.
    -   The USAGE permission on the user-defined types of the database is granted to the PUBLIC group.
-   Examples

    ```
    CALL spm_disable ();
    ```


## spm\_cleanup

-   Description

    spm\_cleanup: deletes the <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups of a database after the SPM is disabled.

    **Note:** To call the spm\_cleanup function, you must be a superuser.

-   Syntax

    ```
    CALL spm_cleanup ( db_name [, batch_size ] );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|Value range|
    |---------|-----------|-----------|
    |db\_name|The name of the database for which you want to delete user groups. If the database name contains special characters or uppercase letters, enclose it in double quotation marks \(" "\), such as `"MYDB"`.|N/A|
    |batch\_size|The maximum number of objects whose ownership can be transferred at a time.If you set this parameter to 0, the maximum number of objects whose ownership can be transferred at a time is specified by the [max\_locks\_per\_transaction](https://www.postgresql.org/docs/9.1/runtime-config-locks.html) parameter.

|The value of the batch\_size parameter must fall into the closed interval \[0, max\_locks\_per\_transaction\]. Otherwise, this parameter is invalid.If the number of existing objects in the database exceeds the value of the batch\_size parameter, you must repeat calling the spm\_migrate function until the ownership of all the objects is transferred.

In addition, we recommend that you [submit a ticket](https://workorder-intl.console.aliyun.com/) to increase the value of the max\_locks\_per\_transaction parameter before you call the spm\_cleanup function. |

-   Usage notes
    -   After you call the spm\_cleanup function in a database, if the `DONE BUT NOT COMPLETED` message is returned, the ownership of part of objects is not transferred. You must call the spm\_cleanup function again.
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

    spm\_alter\_database\_rename: renames a database.

    After you rename a database for which the SPM is enabled, the names of the <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups are automatically modified. You must call the spm\_alter\_database\_rename function to rename a database when the SPM is enabled.

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
|spm\_enable|Enables the SPM.|Granted|Not granted|Not granted|
|spm\_disable/spm\_enable|Disables the SPM.|Granted|Not granted|Not granted|
|spm\_grant|Adds a user to a user group.|Granted|Granted|Not granted|
|spm\_revoke|Removes a user from a user group.|Granted|Granted|Not granted|
|spm\_\_alter\_database\_rename|Renames a database. After you call this function in a database, the schemas and user groups of the database are automatically modified.|Granted|Granted|Not granted|
|spm\_migrate|Transfers the ownership of all tables and table-like objects in a database to the specified developers after the SPM is enabled for the database.|Granted|Granted|Not granted|
|spm\_cleanup|Deletes all user groups of a database.|Granted|Not granted|Not granted|
|spm\_create\_user|Creates a user in a Hologres instance and grants the user only the permission to connect to the instance.|Granted|Granted|Not granted|

## Restricted statements in the SPM

The SPM limits the usage of part of statements related to permissions, as described in the following table.

|Statement|Description|
|---------|-----------|
|alter table owner to xx|After you enable the SPM for a database, the ownership of all tables under schemas of the database is automatically transferred to the members of the <db\>\_developer group. You are not allowed to transfer the ownership of these tables.|
|grant|You can call the `spm_grant` function to add a user to a user group. After that, you no longer need to execute the GRANT statement.|
|revoke|You can call the `spm_revoke` function to remove a user from a user group. After that, you no longer need to execute the REVOKE statement.|
|alter default privileges|In the standard PostgreSQL authorization model, you can grant users the permissions only on existing tables and objects. To enable users to manage new tables and objects, you must grant them permissions again. In the SPM, you can add users to user groups to grant them the required permissions on all tables or objects of the specified types, including the existing ones and new ones.|
|create role/drop role/alter role/alter role set default user groups|You are not allowed to execute these statements to manage the <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups. These user groups are generated by the system when the SPM is enabled and cannot be created or modified by users, including superusers.|
|rename to/from default user groups|You are not allowed to execute these statements to rename the `<db>_admin, <db>_developer, <db>_writer, and <db>_viewer` groups. These user groups are generated by the system when the SPM is enabled.|
|rename database|You are not allowed to execute this statement to rename a database for which the SPM is enabled. Instead, you can call the `spm_alter_database_rename` function to rename the database.|

