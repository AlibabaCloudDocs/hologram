---
keyword: [schema, SLPM, function calls]
---

# Functions of the SLPM

This topic describes the functions of the schema-level permission model \(SLPM\) in Hologres. You can call these functions to manage the SLPM.

## Overview

The SLPM supports the following functions:

-   [slpm\_enable](#section_kek_wve_iby): enables the SLPM.
-   [slpm\_migrate](#section_649_pj0_ge6): migrates existing objects such as tables, views, and foreign tables to the SLPM.
-   [slpm\_create\_user](#section_4s9_3o6_85l): creates a user in the SLPM.
-   [slpm\_grant](#section_xjb_43p_lt9): adds a user to the \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, or \{db\}.\{schema\}.viewer group.
-   [slpm\_revoke](#section_2yh_r1n_nkk): removes a user from the \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, or \{db\}.\{schema\}.viewer group.
-   [slpm\_disable](#section_lgu_f40_xx6): disables the SLPM for a database.
-   [slpm\_cleanup](#section_aen_1tr_jzt): deletes the \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, and \{db\}.\{schema\}.viewer groups that are retained for the SLPM.
-   [slpm\_rename\_database](#section_9ey_05j_kjn): renames a database.
-   [slpm\_rename\_schema](#section_8bl_t53_80u): renames a schema.

## slpm\_enable

-   Description

    slpm\_enable: enables the SLPM.

    After you call the slpm\_enable function, the system automatically creates the following user groups: \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, and \{db\}.\{schema\}.viewer.

-   Syntax

    ```
    CALL slpm_enable ();
    ```

    **Note:** To call the slpm\_enable function, you must be a superuser of the Hologres instance.

-   Usage notes

    After you call the slpm\_enable function to enable the SLPM for a database, the following changes occur to the permissions of users:

    -   All permissions on the database are revoked from the PUBLIC group. This prevents unauthorized users from connecting to the database.
    -   All permissions on all schemas of the database are revoked from the PUBLIC group.
    -   Users in the \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, and \{db\}.\{schema\}.viewer groups have the permission to connect to the database.
    -   Users in the \{db\}.admin group become the owners of the database and all schemas of the database.
    -   Users in the \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, and \{db\}.\{schema\}.viewer groups have the USAGE permission on the schema. Users in the \{db\}.\{schema\}.developer group have the CREATE permission on the schema.
    -   After users in the \{db\}.admin and \{db\}.\{schema\}.developer groups create objects based on a schema, users in the \{db\}.\{schema\}.developer group are owners of these objects. Users in the \{db\}.\{schema\}.writer group have the read and write permissions on these objects. Users in the \{db\}.\{schema\}.viewer group have the read-only permissions on these objects.
    -   After users in the \{db\}.admin and \{db\}.\{schema\}.developer groups create objects that do not have a schema, such as foreign servers, foreign data wrappers, and languages, users in the \{db\}.admin group are owners of these objects. Users in the \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, and \{db\}.\{schema\}.viewer groups have the USAGE permission on these objects.

## slpm\_migrate

-   Description

    slpm\_migrate: migrates existing objects such as tables, views, and foreign tables to the SLPM.

-   Syntax

    ```
    CALL slpm_migrate ( [ batch_size ] );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|Value range|
    |---------|-----------|-----------|
    |batch\_size|The maximum number of objects that can be migrated at a time.If you set this parameter to 0, the maximum number of objects that can be migrated at a time is specified by the [max\_locks\_per\_transaction](https://www.postgresql.org/docs/9.1/runtime-config-locks.html) parameter.

|\[1, 64\]. This parameter is invalid if its value is beyond this range.If your database contains many objects, for example, hundreds of objects, you must call the slpm\_migrate function multiple times until all objects are migrated.

If your database contains a large number of objects, for examples, more than thousands or even tens of thousands of objects, we recommend that you [submit a ticket](https://workorder-intl.console.aliyun.com/) to increase the value of the max\_locks\_per\_transaction parameter before you migrate objects. |

-   Usage notes
    -   If the `DONE BUT NOT COMPLETED` message is returned after you call the slpm\_migrate function, you still have some objects to migrate. In this case, call the slpm\_migrate function again.
    -   If the `COMPLETED` message is returned, all objects are migrated.
-   Examples

    ```
    CALL slpm_migrate (); // Migrate objects to the SLPM. The maximum number of objects that can be migrated is specified by the max_locks_per_transaction parameter.
    
    CALL slpm_migrate (64); // Migrate a maximum of 64 objects to the SLPM.
    ```


## slpm\_create\_user

-   Description

    slpm\_create\_user: creates a user in the SLPM. After you call this function to create a user, the user has only the logon permission. To allow the user to perform data analytics, you must grant specific permissions to the user.

    **Note:** To call the slpm\_create\_user function, you must be a superuser or a member of the \{db\}.admin group of the database.

-   Syntax

    ```
    CALL slpm_create_user ( user_name [, role_name] );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|
    |---------|-----------|
    |user\_name|The name of the user to be created. You can specify the name in one of the following formats:    -   The ID or display name of an Alibaba Cloud account, such as 13432193xxxx or xx@aliyun.com.
    -   The UID of a RAM user prefixed with p4\_ or the display name of the RAM user, such as p4\_202338382183xxx or RAM$mainaccount:subuser. |
    |role\_name|The group to which the user is to be added. You can add the user to one of the following groups based on your business needs:    -   \{db\}.admin
    -   \{db\}.\{schema\}.developer
    -   \{db\}.\{schema\}.writer
    -   \{db\}.\{schema\}.viewer
For more information about the permissions of user groups, see [User groups and permissions in the SLPM](/intl.en-US/User Authorization/Hologres permission models/SLPM/Overview.md).|

-   Examples

    ```
    CALL slpm_create_user ('my_test@aliyun.com');
    CALL slpm_create_user ('RAM$my_test:mysubuser', 'mydb.public.developer');
    CALL slpm_create_user ('13532313103042xxx');
    CALL slpm_create_user ('p4_23319103042xxx', 'mydb.admin');
    ```


## slpm\_grant

-   Description

    slpm\_grant: adds a user to the \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, or \{db\}.\{schema\}.viewer group.

    **Note:** To call the slpm\_grant function, you must be a superuser or a member of the \{db\}.admin group of the database.

-   Syntax

    ```
    CALL slpm_grant ( role_name, user_name );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|
    |---------|-----------|
    |user\_name|The name of the user to which you want to grant permissions. You can specify the name in one of the following formats:    -   The ID or display name of an Alibaba Cloud account, such as 13432193xxxx or xx@aliyun.com.
    -   The UID of a RAM user prefixed with p4\_ or the display name of the RAM user, such as p4\_202338382183xxx or RAM$mainaccount:subuser. |
    |role\_name|The group to which the user is to be added. You can add the user to one of the following groups based on your business needs:    -   \{db\}.admin
    -   \{db\}.\{schema\}.developer
    -   \{db\}.\{schema\}.writer
    -   \{db\}.\{schema\}.viewer
For more information about the permissions of user groups, see [User groups and permissions in the SLPM](/intl.en-US/User Authorization/Hologres permission models/SLPM/Overview.md).|

-   Usage notes
    -   You can call this function only after the SLPM is enabled.
    -   The value of the user\_name parameter must be the account ID or display name of an Alibaba Cloud account or a RAM user.
    -   The value of the role\_name parameter must be in one of the following formats: \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, or \{db\}.\{schema\}.viewer.
-   Examples

    ```
    CALL slpm_grant ('mydb.public.developer', 'p4_202338382183xxx'); // Add the specified RAM user to the mydb.public.developer group.
    CALL slpm_grant ('mydb.admin', 'RAM$my_test:xxx'); // Add the specified RAM user to the mydb.admin group.
    CALL slpm_grant ('otherdb.admin', '13532313103042xxx'); // This function fails because you can add a user only to a group of the current database. To add a user to a group of another database, connect to the database and call the slpm_grant function.
    ```


## slpm\_revoke

-   Description

    slpm\_revoke: removes a user from the \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, or \{db\}.\{schema\}.viewer group.

    **Note:** To call the slpm\_revoke function, you must be a superuser or a member of the \{db\}.admin group of the database.

-   Syntax

    ```
    CALL slpm_revoke ( role_name, user_name );
    ```

-   Usage notes
    -   You can call this function only after the SLPM is enabled.
    -   The value of the user\_name parameter must be the account ID or display name of an Alibaba Cloud account or a RAM user.
    -   The value of the role\_name parameter must be in one of the following formats: \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, or \{db\}.\{schema\}.viewer.
-   Examples

    ```
    CALL slpm_revoke ('mydb.public.developer', 'p4_202338382183xxx'); // Remove the specified RAM user from the mydb.public.developer group.
    CALL slpm_revoke ('mydb.admin', 'RAM$my_test:xxx'); // Remove the specified RAM user from the mydb.admin group.
    CALL slpm_revoke ('otherdb.admin', '13532313103042xxx'); // This function fails because you can remove a user only from a group of the current database. To remove a user from a group of another database, connect to the database and call the slpm_revoke function.
    ```


## slpm\_disable

-   Description

    slpm\_disable: disables the SLPM for a database.

    **Note:** To call the slpm\_disable function, you must be a superuser.

-   Syntax

    ```
    CALL slpm_disable ();
    ```

-   Usage notes

    When you disable the SLPM, take note of the following items:

    -   The \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, and \{db\}.\{schema\}.viewer groups are retained. You can execute authorization statements in the standard PostgreSQL authorization model to add users to these user groups. Users in the \{db\}.\{schema\}.developer group are still the owners of database objects.
    -   The CONNECT and TEMPORARY permissions on the database are granted to the PUBLIC group.
    -   The USAGE and CREATE permissions on the public schema of the database are granted to the PUBLIC group.
    -   The EXECUTE permission on the functions and procedures of the public schema in the database is granted to the PUBLIC group.
    -   The USAGE permission on the user-defined languages of the database is granted to the PUBLIC group.
    -   The USAGE permission on the user-defined types of the database is granted to the PUBLIC group.
-   Examples

    ```
    CALL slpm_disable ();
    ```


## slpm\_cleanup

-   Description

    slpm\_cleanup: deletes the \{db\}.admin, \{db\}.\{schema\}.developer, \{db\}.\{schema\}.writer, and \{db\}.\{schema\}.viewer groups that are retained for the SLPM.

    **Note:** To call the slpm\_cleanup function, you must be a superuser.

-   Syntax

    ```
    CALL slpm_cleanup ( db_name [, batch_size ] );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|Value range|
    |---------|-----------|-----------|
    |db\_name|The name of the database for which you want to delete user groups.|If the name contains special characters or uppercase letters, enclose it in double quotation marks \(" "\), such as `"MYDB"`.|
    |batch\_size|The maximum number of objects that can be migrated at a time.If you set this parameter to 0, the maximum number of objects that can be migrated at a time is specified by the [max\_locks\_per\_transaction](https://www.postgresql.org/docs/9.1/runtime-config-locks.html) parameter. The default value of this parameter is 64.

|\[0, 64\]. This parameter is invalid if its value is beyond this range.If your database contains many objects, for example, hundreds of objects, you must call the slpm\_migrate function multiple times until all objects are migrated.

If your database contains a large number of objects, for examples, more than thousands or even tens of thousands of objects, we recommend that you [submit a ticket](https://workorder-intl.console.aliyun.com/) to increase the value of the batch\_size parameter before you migrate objects. |

-   Usage notes
    -   If the `DONE BUT NOT COMPLETED` message is returned after you call the slpm\_cleanup function, you still have some objects to migrate, and the user groups are not deleted. In this case, call the slpm\_cleanup function again.
    -   If the `COMPLETED` message is returned, all objects are migrated and the user groups are deleted.
-   Examples

    ```
    CALL slpm_cleanup ('mydb'); // Change the owner of objects to the current user. The maximum number of objects for which you can change the owner is specified by the max_locks_per_transaction parameter.
    CALL slpm_cleanup ('mydb', 64); // Change the owner of up to 64 objects to the current user.
    ```

    -   Case 1: Delete a database and then user groups of the database.

        ```
        drop database mydb;
        CALL slpm_cleanup ('mydb'); // You can delete user groups of a deleted database at a time.
        ```

    -   Case 2: Delete user groups of an existing database. In this case, you must connect to the database.

        ```
         CALL slpm_cleanup ('otherdb');
        ERROR:  Permission Denied. execute in database otherdb, or drop database before call slpm_cleanup.
        ```


## slpm\_rename\_database

-   Description

    slpm\_rename\_database: renames a database.

    After you rename a database for which the SLPM is enabled, you must also rename the four user groups. The slpm\_rename\_database function automatically renames the user groups. Therefore, you must call this function to rename a database.

    **Note:** To call the slpm\_rename\_database function, you must be a superuser or a member of the \{db\}.admin group of the database.

-   Syntax

    ```
    CALL slpm_rename_database ( old_name, new_name );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|
    |---------|-----------|
    |old\_name|The original name of the database to be renamed. If the name contains special characters or uppercase letters, enclose it in double quotation marks \(" "\), such as `"MYDB"`.|
    |new\_name|The new name that you want to specify for the database. If the name contains special characters or uppercase letters, enclose it in double quotation marks \(" "\), such as `"MYDB"`.|

-   Examples

    ```
    CALL slpm_rename_database ('olddb', 'newdb');  
    ```


## slpm\_rename\_schema

-   Description

    slpm\_rename\_schema: renames a schema.

    After you rename a database for which the SLPM is enabled, you must also rename the four user groups. The slpm\_rename\_schema function automatically renames the user groups. Therefore, you must call this function to rename a schema.

    **Note:** To call the slpm\_rename\_schema function, you must be a superuser or a member of the \{db\}.admin group of the database.

-   Syntax

    ```
    CALL slpm_rename_schema ( old_name, new_name );
    ```

    The following table describes the parameters in the syntax.

    |Parameter|Description|
    |---------|-----------|
    |old\_name|The original name of the schema to be renamed. If the name contains special characters or uppercase letters, enclose it in double quotation marks \(" "\), such as `"MYDB"`.|
    |new\_name|The new name that you want to specify for the schema. If the name contains special characters or uppercase letters, enclose it in double quotation marks \(" "\), such as `"MYDB"`.|

-   Examples

    ```
    CALL slpm_rename_schema ('oldschema', 'newschema');  
    ```


