---
keyword: [最佳实践, 专家权限模型, 标准授权]
---

# 基于PostgreSQL标准权限模型授权

本文为您介绍在Hologres中，如何基于PostgreSQL标准模型（专家权限模型）进行授权的最佳实践。帮助您简化授权操作并使用更细粒度的权限管理。

Hologres兼容PostgreSQL生态，支持PostgreSQL的标准权限模型（简称专家权限模型）。同时，Hologers提供了一套简单权限模型的授权模式，详情请参见[简单权限模型](https://help.aliyun.com/document_detail/158698.html?spm=a2c4g.11174283.6.596.2411729fVnA6oV)。

简单权限模型的权限划分粒度比较粗，不太适用于细粒度的权限管理场景。PostgreSQL的标准授权对于权限的划分非常细致，如果您需要使用更细粒度的权限管理，请参照本文基于PostgreSQL授权的最佳实践进行操作。

## PostgreSQL权限模型简介

标准PostgreSQL授权拥有详细的权限管理体系，详情请参见[Postgresql授权](https://www.postgresql.org/docs/11/sql-createuser.html)。

PostgreSQL授权的限制如下：

-   PostgreSQL授权仅支持对现有对象授权，对未来的对象不生效。示例如下。
    1.  User1执行了`GRANT SELECT ON ALL TABLES IN SCHEMA public TO User2;`语句对User2授予了**public** Schema中所有表的SELECT权限。
    2.  User1在**public** Schema中创建了一张新表table\_new。
    3.  User2执行`SELECT * FROM table_new`语句时显示报错`Permission denied`。

        由于User1对User2授予SELECT权限时，仅包含授权时刻**public** Schema中的所有表，而不包含未来在**public** Schema中创建的表，因此会产生上述报错。

-   您可以使用`ALTER DEFAULT PRIVILEGES`语句，对未来对象设置默认权限，详情请参见[ALTER DEFAULT PRIVILEGES](https://www.postgresql.org/docs/11/sql-alterdefaultprivileges.html)。该权限仅对未来对象生效。示例语句如下。

    ```
    ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO PUBLIC; //默认所有人对在**public** Schema中新创建的表具有读权限。
    ```

    您也可以使用`ALTER DEFAULTPRIVILEGES FOR ROLE xxx`语句，为其他角色创建默认权限。当前用户和xxx满足以下关系时才能成功设置默认权限：

    -   当前用户是xxx组的成员。
    -   当前用户是超级用户（Superuser）时，xxx可以为用户或组。
    您可以使用psql命令`\ddp`查看（系统表为`pg_catalog.pg_default_acl`）`ALTER DEFAULT PRIVILEGES`是否设置成功。

    `ALTER DEFAULT PRIVILEGES`类似于一个触发器。创建新表时，Hologres会使用当前用户和Schema去检查`pg_catalog.pg_default_acl`系统表中是否有匹配项。如果存在匹配项，则系统自动添加匹配项规则。

    **说明：**

    -   您只能使用当前用户与匹配项规则进行匹配，不能使用当前用户的用户组去匹配。
    -   `alter default privileges`规则匹配只能在创建表时执行，在创建表之后修改表Owner（ `alter table set owner to`）不会触发`alter default privileges`。
    例如，用户User1属于Group1，如果要给Group1匹配规则，授予未来表全权限。情况如下：

    -   如果当前用户是User1，则创建表时匹配不到规则。
    -   如果创建表之前先执行`set session role group1;`，改变当前用户为Group1，则创建表时就可以匹配到规则，系统自动为新创建的表授权。
-   只有表Owner才可以删除表。

    在标准的PostgreSQL授权语句中，您需要根据Ownership来判断是否可以删除表。可以删除表的角色如下：

    -   表的Owner（即建表者）。
    -   表所在Schema的Owner。
    -   Superuser
-   在PostgreSQL中，系统会默认创建表的用户为表的Owner，拥有该表的所有权限，包括删除表的权限。

    修改表Owner，示例如下语句。

    ```
    alter table <table> owner to user2; //将表Owner由User1修改为User2。
    alter table <table> owner to GROUP1;//将表Owner修改为Group1。
    ```

    修改表Owner的操作限制如下：

    -   User1为表的Owner。
    -   User1必须是Group1的直接或者间接成员。

        例如，User1是Group1的成员，或User1是Group1中某个组的成员。

    -   Group1必须在表所在的Schema中有创建表的权限。
    -   Superuser可以修改任意表的表Owner。

## PostgreSQL权限模型规划

标准的PostgreSQL（专家权限模型）权限粒度划分比较细致，在使用之前需要对现有实例对象做如下权限规划：

-   总共有多少个权限组。
-   每个组的作用是什么。
-   每个组包含哪些用户。
-   哪些角色在什么时候可以删除表。
-   每个组在哪些Schema中工作。

建议您执行如下操作，规划实例对象：

-   确定权限组以及组的作用。

    权限组分为以下类型：

    -   XX\_DEV\_GROUP：表的Owner，拥有表的所有操作权限。
    -   XX\_WRITE\_GROUP：表的写入权限，可以写入数据至相应表。
    -   XX\_VIEW\_GROUP：查看表数据权限，可以查看相应表的数据。
    XX表示一个项目。例如PROJ1项目的权限组包括PROJ1\_DEV\_GROUP、 PROJ1\_WRITE\_GROUP及PROJ1\_VIEW\_GROUP。

    **说明：** 组名的命名规范仅为建议参考，不做强制要求。

-   确定权限组所在的Schema。

    推荐每个项目的权限组使用一个Schema。

    一个DEV\_GROUP可以拥有多张表，但每张表只能属于一个DEV\_GROUP。例如，表TABLE1属于PROJ1\_DEV\_GROUP，则该表就不属于PROJ2\_DEV\_GROUP。

    一个用户可以属于多个DEV\_GROUP。例如，User1可以既是PROJ1\_DEV\_GROUP的成员，也可以是PROJ2\_DEV\_GROUP的成员。


## Hologres专家权限模型最佳实践一

本次实践以表对象为例，您也可以选用其他对象进行实验。

表的Owner是对应的XXX\_DEV\_GROUP权限组，因此，DEV\_GROUP组中的任意成员都可以管理或删除该表。

例如，用户被添加到PROJ1\_DEV\_GROUP用户组之后，就拥有PROJ1项目中表的管理或删除权限。具体操作步骤如下：

1.  创建用户组。

    您可以根据业务需求划分权限模型，由Superuser创建相应的用户组，以PROJ1项目为例，示例语句如下。

    ```
    create role PROJ1_DEV_GROUP; //表的Owner，拥有表的所有操作权限。
    create role PROJ1_WRITE_GROUP; //表的写入权限，可以写入数据至相应表。
    create role PROJ1_VIEW_GROUP; //查看表数据权限，可以查看相应表的数据。
    ```

2.  授权用户组Schema的权限。

    您需要授予创建完成的用户组Schema的权限，示例项目PROJ1可以在Schema1中工作，语句如下。

    ```
    授权PROJ1拥有SCHEMA1中的相关权限。
    grant create,usage on schema SCHEMA1 to PROJ1_DEV_GROUP;
    grant usage on schema SCHEMA1 to PROJ1_WRITE_GROUP;
    grant usage on schema SCHEMA1 to PROJ1_VIEW_GROUP;
    ```

    **说明：**

    -   一个项目可以对应多个Schema，一个Schema也可以对应多个项目。
    -   默认**public** Schema中所有用户都有CREATE和USAGE权限。
3.  创建用户并管理用户组。

    授权用户组Schema权限后，Superuser需要创建用户并添加用户至对应的用户组，示例语句如下。

    ```
    create user "USER1";
    grant PROJ1_DEV_GROUP to "USER1"; 
    
    create user "USER2";
    grant PROJ1_VIEW_GROUP to "USER2";
    ```

4.  创建表并授权。

    创建表等对象时，由表的创建者（必须为PROJ1\_DEVE\_GROUP的成员）或Superuser执行相应的授权语句（假设新创建的表为TABLE1）。 示例如下。

    ```
    grant all on table SCHEMA1.TABLE1 to PROJ1_WRITE_GROUP; //授予PROJ1_WRITE_GROUP写入数据至TABLE1的权限。
    grant select on table SCHEMA1.TABLE1 to PROJ1_VIEW_GROUP; //授予PROJ1_VIEW_GROUP TABLE1的SELECT权限。
    alter table SCHEMA1.TABLE1 owner to PROJ1_DEV_GROUP; //修改TABLE1的Owner为PROJ1_DEV_GROUP。
    ```


## Hologres专家权限模型最佳实践二

本次实践，使用`ALTER DEFAULT PRIVILEGES`语句简化对每张表的授权操作。

您需要提前确定创建的表默认属于哪个项目。具体操作步骤如下：

1.  创建用户组。

    您可以根据业务需求划分权限模型，由Superuser创建相应的用户组，以PROJ1项目为例，示例语句如下。

    ```
    create role PROJ1_DEV_GROUP; //表的Owner，拥有表的所有操作权限。
    create role PROJ1_WRITE_GROUP; //表的写入权限，可以写入数据至相应表。
    create role PROJ1_VIEW_GROUP; //查看表数据权限，可以查看相应表的数据。
    ```

2.  授权用户组Schema的权限。

    您需要授予创建完成的用户组Schema的权限，示例项目PROJ1可以在Schema1中工作，语句如下。

    ```
    授权PROJ1拥有SCHEMA1中的相关权限。
    grant create,usage on schema SCHEMA1 to PROJ1_DEV_GROUP;
    grant usage on schema SCHEMA1 to PROJ1_WRITE_GROUP;
    grant usage on schema SCHEMA1 to PROJ1_VIEW_GROUP;
    ```

    **说明：**

    -   一个项目可以对应多个Schema，一个Schema也可以对应多个项目。
    -   默认**public** Schema中所有用户都有CREATE和USAGE权限。
3.  创建用户并设置默认授权。

    完成Schema授权后，需要Superuser创建用户并添加用户至相应的组中，同时设置该用户创建表时拥有的默认权限。

    USER1创建的表默认属于PROJ1\_DEV\_GROUP，并且USER1为合法的阿里云账号。示例设置授权语句如下。

    ```
    create user "USER1";
    
    alter default privileges for role "USER1" grant all on tables to PROJ1_DEV_GROUP; //设置USER1创建的表，PROJ1_DEV_GROUP默认都有读写权限。
    alter default privileges for role "USER1" grant all on tables to PROJ1_WRITE_GROUP; //设置USER1创建的表，PROJ1_WRITE_GROUP默认都有读写权限。
    alter default privileges for role "USER1" grant select on tables to PROJ1_VIEW_GROUP; //设置USER1创建的表，PROJ1_VIEW_GROUP默认都有读写权限。
    
    grant PROJ1_DEV_GROUP to "USER1"; //添加USER1至PROJ1_DEV_GROUP。
    ```

4.  修改表的Owner。

    如果您希望DEV\_GROUP中的其他用户也可以管理或删除创建的表，则可以修改表的Owner为对应项目的DEV\_GROUP，例如PROJ1\_DEV\_GROUP。

    修改表Owner的语句必须由或Superuser执行。例如，示例中表的创建者必须是PROJ1\_DEV\_GROUP的成员。假设新创建的表为TABLE1。示例修改表Owner的语句如下。

    ```
    alter table SCHEMA1.TABLE1 owner to PROJ1_DEV_GROUP; //修改TABLE1的Owner为PROJ1_DEV_GROUP。
    ```

    如下情况，您可以修改表的Owner：

    -   新创建的表，由Superuser定期修改表Owner。
    -   在需要管理或删除表之前修改表Owner。
    -   如果您可以确认表的管理或删除操作是由表的创建者或Superuser执行的，您也可以不执行上述命令。
5.  修改用户的默认项目。

    调整用户的默认项目，需要Superuser或用户本人执行`alter default privileges`命令，撤销已设置的缺省权限后，使用新的`alter default privileges`创建默认授权。

    将USER1的默认项目由PROJ1改为PROJ2，该操作不影响现有的表。示例语句如下。

    ```
    取消原有默认授权。
    alter default privileges for role "USER1" revoke all on tables from PROJ1_DEV_GROUP;
    alter default privileges for role "USER1" revoke all on tables from PROJ1_WRITE_GROUP;
    alter default privileges for role "USER1" revoke select on tables from PROJ1_VIEW_GROUP;
    
    创建新的默认授权。
    alter default privileges for role "USER1" grant all on tables to PROJ2_DEV_GROUP;
    alter default privileges for role "USER1" grant all on tables to PROJ2_WRITE_GROUP;
    alter default privileges for role "USER1" grant select on tables to PROJ2_VIEW_GROUP;
    ```


