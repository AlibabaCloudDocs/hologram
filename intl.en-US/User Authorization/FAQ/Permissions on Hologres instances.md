---
keyword: [Hologres, development permissions on a Hologres instance]
---

# Permissions on Hologres instances

When you perform data analytics operations on a Hologres instance, an error may occur if you are not granted the required development permissions. This topic provides answers to commonly asked questions about the development permissions on Hologres instances.

## FAQ

You can perform data analytics operations on a purchased Hologres instance only after you are granted specific development permissions on the instance. To view the FAQ about permissions on the instance, click the following links:

-   [How do I select an appropriate Hologres permission model?](#section_0fa_ll5_xi4)
-   [Why is the error message "role "RAM$xxx" doesn't not exsit" returned when I perform data analytics operations on a Hologres instance?](#section_p5q_1as_7nu)
-   [Why is the error message "Cloud authentication failed for access id "xxxx"" returned when I connect to an instance by using a development tool?](#section_l8p_ucf_v8g)
-   [Why is the error message "permission denied for table xxxx" returned when I query a table?](#section_mwo_pph_wmh)
-   [Why is the error message "permission denied for database"xxx"" returned when I perform data analytics operations on a Hologres instance?](#section_oqr_45c_kin)
-   [Why is the error message "must be the owner of table xxxx" returned when I manage a table?](#section_jst_an7_8a4)

## How do I select an appropriate Hologres permission model?

Hologres allows you to use the standard PostgreSQL authorization model, the simple permission model \(SPM\), or the schema-level permission model \(SLPM\). This section describes how to select an appropriate Hologres permission model based on the following scenarios:

-   The standard PostgreSQL authorization model is a native PostgreSQL permission model. If you are familiar with PostgreSQL and relevant permission management, you can grant permissions to users by using this model with ease. If you need to manage permissions on tables and have sufficient time and energy, you can grant each user the permissions to manage tables and revoke the permissions by using this model.
-   The SPM is a database-level simple permission model. In this model, all users who need to use a database are added to a specific user group. Each user group are granted specific access permissions on all objects in a schema. You may rarely perform data development based on schemas or only use schemas to classify table objects just like directories without the need to isolate permissions based on the schemas. In this case, we recommend that you use the SPM.
-   In the SLPM, each schema includes a developer group, a writer group, and a viewer group. If you need to perform the schema-level user isolation and permission isolation for tables, we recommend that you use the SLPM.

## Why is the error message "role "RAM$xxx" doesn't not exsit" returned when I perform data analytics operations on a Hologres instance?

-   Problem description

    After I connected to an instance by using a development tool, I could not perform data analytics operations such as query data in the instance. The following error message is returned: role "RAM$xxx" doesn't not exsit.

-   Cause

    The current RAM user is not added to the instance.

-   Solution

    You can grant the current RAM user relevant permissions on the instance based on your business needs. For example, you can assign the superuser role to the RAM user. For more information about authorization, see [Grant the development permissions on a Hologres instance to RAM users](/intl.en-US/User Authorization/Authorization operations/Grant the development permissions on a Hologres instance to RAM users.md).


## Why is the error message "Cloud authentication failed for access id "xxxx"" returned when I connect to an instance by using a development tool?

-   Problem description

    When I connected to an instance by using a development tool, an error occurred. The following error message is returned: Cloud authentication failed for access id "xxxx".

-   Cause

    The password of the current RAM user is invalid.

-   Solution

    You must check the password of the current RAM user to ensure that the password is an AccessKey pair and does not contain spaces. For more information about the AccessKey pair, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).


## Why is the error message "permission denied for table xxxx" returned when I query a table?

-   Problem description

    When I performed data analytics operations such as query data in an instance, an error occurred. The following error message is returned: Execution failed: ERROR: permission denied for table xxxx.

-   Cause

    The current RAM user does not have permissions to view specific tables.

-   Solution

    You can select solutions based on different authorization models.

    -   Standard PostgreSQL authorization model: Execute the following statement to grant permissions to a RAM user. In the statement, p4\_UID specifies the account information of the RAM user. For more information about the standard PostgreSQL authorization model, see [Grant permissions by using the standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres Permission Model/Grant permissions by using the standard PostgreSQL authorization model.md).

        ```
        grant select on table tablename to "p4_UID";
        ```

    -   SPM: Add the current RAM user to a viewer group or another user group. For more information, see [Use the SPM](/intl.en-US/User Authorization/Hologres Permission Model/SPM/Use the SPM.md).

## Why is the error message "permission denied for database"xxx"" returned when I perform data analytics operations on a Hologres instance?

-   Problem description

    After I connected to an instance by using a development tool, I could not perform data analytics operations such as query data in the instance. The following error message is returned: FATAL:permission denied for database"xxx" detail: user does not have CONNECT privilege.

-   Cause

    The current RAM user is only added to the instance but is not granted the development permissions on the instance.

-   Solution

    You can grant the current RAM user relevant permissions on the instance based on your business needs. For example, you can assign the superuser role to the RAM user. For more information about authorization, see [Grant the development permissions on a Hologres instance to RAM users](/intl.en-US/User Authorization/Authorization operations/Grant the development permissions on a Hologres instance to RAM users.md).


## Why is the error message "must be the owner of table xxxx" returned when I manage a table?

-   Problem description

    When I performed data analytics operations on an instance, an error occurred. The following error message is returned: must be the owner of table xxxx.

-   Cause

    The current RAM user is not the owner of the table and is not allowed to create a child partitioned table or delete the table.

-   Solution

    You can select solutions based on different authorization models.

    -   Standard PostgreSQL authorization model: Execute the following statement to grant owner permissions of the table to the current RAM user. In the statement, p4\_UID specifies the account information of the RAM user. For more information about the standard PostgreSQL authorization model, see [Grant permissions by using the standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres Permission Model/Grant permissions by using the standard PostgreSQL authorization model.md).

        ```
        alter table tablename owner to "p4_UID";
        ```

    -   SPM: Add the current RAM user to a developer group or another user group. For more information, see [Use the SPM](/intl.en-US/User Authorization/Hologres Permission Model/SPM/Use the SPM.md).

