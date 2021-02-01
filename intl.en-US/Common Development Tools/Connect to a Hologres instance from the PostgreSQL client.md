---
keyword: [Hologres, PostgreSQL client]
---

# Connect to a Hologres instance from the PostgreSQL client

This topic describes how to connect to a Hologres instance and use the standard PostgreSQL statements for data analytics from the PostgreSQL client.

1.  The PostgreSQL client is installed. If you have not installed the client, go to the [PostgreSQL official website](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads), download the installation package of version 11 or later based on your operating system, and then install the client as prompted.
2.  The environment variables of your operating system are set. To set the environment variables, perform the following steps:

    -   Procedure for the Windows operating system
        1.  Open the **System Properties** dialog box. Click the **Advanced** tab and then click **Environment Variables** in the lower-right corner.
        2.  Set the Path variable to the bin subdirectory of the installation directory.
        3.  Click **OK**.
    -   For more information about how to set the environment variables in the macOS operating system, see [Setting Up Your Environment](https://www.postgresql.org/docs/6.3/c0301.htm).
    **Note:** You do not need to set environment variables in the Linux operating system.


1.  Open the PostgreSQL client and execute the required SQL statements based on your operating system to connect to Hologres.

    -   Execute the following SQL statement if you are using the Linux operating system:

        ```
        psql -h <Endpoint> -p <Port> -U <AccessKey ID> -d <Database>
        ```

        Enter the AccessKey secret of your Alibaba Cloud account as prompted.

        ![LIN](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2729189951/p143892.png)

    -   Execute the following SQL statement if you are using the macOS operating system:

        ```
        PGUSER=<AccessKey ID> PGPASSWORD=<AccessKey Secret> psql -p <Port> -h <Endpoint> -d <Database>
        ```

        ![linux](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3161979951/p137096.png)

    -   Execute the following SQL statements if you are using the Windows operating system:

        ```
        Server [localhost]: Endpoint
        Database [postgres]: Database
        Port [5432]: Port
        Username [postgres]: <AccessKey ID>
        Password of the <AccessKey ID> user: <AccessKey Secret>
        ```

        ![tu](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1379189951/p165824.png)

    |Parameter|Description|
    |---------|-----------|
    |AccessKey ID|The AccessKey ID of your Alibaba Cloud account.You can obtain the AccessKey ID in the [User Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) console. |
    |AccessKey Secret|The AccessKey secret of your Alibaba Cloud account.You can obtain the AccessKey secret in the [User Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) console. |
    |Port|The public port number of the Hologres instance.Example: `80`. |
    |Endpoint|The public endpoint of the Hologres instance.Example: `xxx-cn-hangzhou.hologres.aliyuncs.com`. |
    |Database|The name of the database that you want to access from the PostgreSQL client.After you purchase a Hologres instance, a database named **postgres** is created by default.

You can connect to the **postgres** database, but limited resources are allocated to this database. We recommend that you go to the Hologres console and create a database for business purposes. For more information, see [Create a database](/intl.en-US/Quick Start/Create a database.md).

Example: `mydb`. |


After you connect to the Hologres instance from the PostgreSQL client, you can create databases from the client by using the standard PostgreSQL statements. For more information, see [Use Hologres from the PostgreSQL client](/intl.en-US/Quick Start/Use Hologres from the PostgreSQL client.md).

