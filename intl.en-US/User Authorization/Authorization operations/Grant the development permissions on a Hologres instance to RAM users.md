---
keyword: [RAM user within an Alibaba Cloud account, authorization, perform data analytics operations on a Hologres instance, RAM user]
---

# Grant the development permissions on a Hologres instance to RAM users

This topic describes how to use the simple permission model \(SPM\) or the standard PostgreSQL authorization model in Hologres to grant the development permissions on a purchased Hologres instance to a RAM user within your Alibaba Cloud account.

By default, the system sets the Alibaba Cloud account that is used to purchase an instance as a superuser of the instance. A superuser has all permissions on an instance. It can create and delete databases, create roles, and grant permissions to roles.

When you use Hologres as a RAM user, take note of the following rules:

-   The RAM user must be authorized by the Alibaba Cloud account before it can use a Hologres instance. The RAM user can also be assigned the superuser role.
-   Even if the RAM user has permissions to purchase instances, the RAM user can perform data analytics operations on a Hologres instance only after relevant development permissions on the instance are granted by the Alibaba Cloud account.

Permissions that are granted in the RAM console and development permissions on instances apply to different scenarios. For more information, see [Grant permissions on Hologres to RAM users](/intl.en-US/User Authorization/Grant permissions on Hologres to RAM users.md).

## Use the SPM to grant development permissions to a RAM user \(Recommended\)

1.  Grant development permissions to the RAM user.

    You can also grant development permissions to a RAM user by executing SQL statements. For more information, see [Use the SPM](/intl.en-US/User Authorization/Hologres Permission Model/SPM/Use the SPM.md).


## Use the standard PostgreSQL authorization model to grant development permissions to a RAM user

To use the standard PostgreSQL authorization model to grant development permissions to a RAM user, perform the following steps:

1.  Add a RAM user.

    Add a RAM user to a Hologres instance. Sample statement:

    ```
    CREATE USER "p4_user ID"; // Specify the UID of the RAM user.
    CREATE USER "p4_user ID" SUPERUSER; // Assign the superuser role to the RAM user.
    ```

2.  Grant development permissions to the RAM user.

    You can use a RAM user to perform data analytics operations on a Hologres instance only after the required development permissions are granted to the RAM user. Sample statements:

    ```
    GRANT SELECT ON TABLE TABLENAME TO "User ID"; // Grant the RAM user the permissions to query tables in the current database.
    GRANT SELECT,INSERT,UPDATE ON ALL TABLES IN SCHEMA PUBLIC TO "p4_user ID"; // Grant the RAM user the permissions to create, modify, and query tables in the current database.
    ```

    **Note:** Only the superusers and owner of a table can delete the table.

    For more information about the authorization by using the standard PostgreSQL authorization model, see [Grant permissions by using the standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres Permission Model/Grant permissions by using the standard PostgreSQL authorization model.md).


## Use Hologres as a RAM user

After a RAM user is granted the permissions to use Hologres, you can connect to the instance that you want to manage from the PostgreSQL client and perform data analytics operations on the instance as the RAM user. For more information, see [Connect to a Hologres instance from the PostgreSQL client](/intl.en-US/Common Development Tools/Connect to a Hologres instance from the PostgreSQL client.md).

Sample statement:

```
PGUSER=<AccessID> PGPASSWORD=<AccessKey> psql -p <Port> -h <Endpoint> -d <Database>
```

## View the permissions of a RAM user

You can view the permissions that are granted to a RAM user in one of the following ways:

-   View the permissions that are granted to the RAM user by executing SQL statements.

    Connect to the Hologres instance to which the RAM user is added from the PostgreSQL client and execute the following SQL statements to view the granted permissions:

    ```
    SELECT * FROM pg_roles WHERE rolname = 'p4_user ID'; // Query the role that is assigned to the specified user.
    SELECT rolname FROM pg_roles;
    SELECT user_display_name(rolname) FROM pg_roles;
    ```


