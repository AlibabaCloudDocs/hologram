---
keyword: [Hologres, 基于Schema级别的简单权限模型, 使用方法, SLPM]
---

# 基于Schema级别的简单权限模型的使用

本文为您介绍在交互式分析Hologres中使用SQL语句实现基于Schema级别的简单权限模型（Schema-level Permission Model，SLPM）的使用方法。

## SLPM的使用限制

由于SLPM是基于Schema级别严格进行权限管控的，因此在授权和使用时需要您注意如下的限制条件：

-   **当您创建一个View或者Rule，引用了跨Schema的两个或多个表时，由于Schema间权限不互通的原因，此View或者Rule将无法被访问。因此不建议您在SLPM管理的DB中创建跨Schema的View或Rule对象。**
-   开启SLPM之后，只会开放特定的权限，开放权限请参见[基于Schema级别的简单权限模型授权](#step_23c_pnm_ph7)。以下表格中的DDL功能将不能在SLPM使用，请您使用对应的SLPM语句执行相关操作，具体函数说明请参见[基于Schema级别的简单权限模型函数说明](/intl.zh-CN/账号与权限管理/Hologres权限模型/基于Schema级别的简单权限模型/基于Schema级别的简单权限模型函数说明.md)。

    |命令语句|说明|SLPM语句|
    |----|--|------|
    |alter table owner to xx|所有table owner都是对应Schema的developer用户组，不支持修改。|无需手动执行。|
    |grant|将用户加入某个用户组后，会被授予对应用户组的权限，无需再执行grant授权语句。|slpm\_grant|
    |revoke|若要移除某个用户的某个权限，只能通过将该用户移除某个用户组的方法来实现，不能单独执行revoke语句。|slpm\_revoke|
    |alter default privileges|在原有权限模型下，授权只对当前以及过去的表有权限，未来表需要重新授权。在简单模型下，无需考虑建表的时态，只需要加入某个用户组就能拥有对应的权限，因此无需再给未来表授权。|无需手动执行。|
    |create / drop / alter / rename默认用户组|，在开启SLPM后，会默认生成admin/developer/writer/viewer等用户组，不允许用户（包括Superuser）对默认用户组进行创建、修改和删除。|不涉及。|
    |rename schema|Schema重命名不支持在某个DB直接执行`alter rename schema`命令，需要调用`slpm_rename_schema`命令实现。|slpm\_rename\_schema|
    |rename database|DB重命名不支持直接执行`alter database rename`命令，需要调用`slpm_rename_database`命令实现。|slpm\_rename\_database|
    |drop database|删除DB需要在执行`drop database`之后，调用`slpm_cleanup('dbName')`来清除默认用户。|需要执行`drop database`之后，调用`slpm_cleanup('dbName')`来清除默认用户。|


## 基于Schema级别的简单权限模型授权

在Hologres实例连接开发工具后，可以使用SQL语句通过基于Schema级别的简单模式对用户进行授权，使该用户具有对应Schema的相关权限。

1.  开启函数调用。

    开启SLPM前，您需要执行如下命令，开启调用函数的开关。示例中的extension是DB维度的，在同一个DB只需执行一次。

    ```
    create extension slpm;
    ```

2.  开启SLPM。

    SLPM默认不开启，需要Superuser在目标DB里执行如下语句进行开启。同时，在开启SLPM时，需要确保当前DB没有正在运行的SQL，否则可能导致开启失败，并对服务产生影响。

    ```
    call slpm_enable ();  // 开启当前DB的SLPM。
    ```

3.  专家权限模型切换为SLPM。

    如果您的DB使用的是专家权限模型，并且DB中包含一定数量的表、视图或外表等对象。此时，如果您需要开启SLPM进行权限管理，可以通过调用slpm\_migrate函数将原用户的权限由专家模型权限切换为SLPM。

    ```
    call slpm_migrate ();  // 将DB中已有的对象change owner到develoepr，使用SLPM管理。
    ```

    在将专家权限模型切换为SLPM时，需要您注意如下内容：

    slpm\_migrate函数默认每次仅对64个（参数可调）用户进行权限切换。如果涉及用户对象数量过多，需要多次执行该函数，直到全部用户权限切换完毕。更多关于该函数的说明，请参见[slpm\_migrate](/intl.zh-CN/账号与权限管理/Hologres权限模型/基于Schema级别的简单权限模型/基于Schema级别的简单权限模型函数说明.mdsection_649_pj0_ge6)。

4.  创建用户至当前实例。

    在为新用户授权之前，您需要将新用户创建至当前实例中。如果新用户已经被创建进实例，可跳过该步骤。

    如下命令中，\{dbname\}.\[admin\|\{schemaname\}.developer\|\{schemaname\}.writer\|\{schemaname\}.viewer\]是将用户加入当前DB中可供选择的用户组名称，更多描述请参见[用户组说明](/intl.zh-CN/账号与权限管理/Hologres权限模型/基于Schema级别的简单权限模型/基于Schema级别的简单权限模型概述.md)。

    ```
    call slpm_create_user ('云账号ID/云邮箱/RAM账号'); // 创建用户，使用云邮箱时还需要加双引号。
    call slpm_create_user ('云账号ID/云邮箱/RAM账号', '{dbname}.[admin|{schemaname}.developer|{schemaname}.writer|{schemaname}.viewer]');  // 创建用户的同时把用户加入对应的用户组。
    ```

    **说明：**

    -   如果使用RAM用户UID账号进行授权，执行`slpm_create_user`时需要在UID前加p4\_，即“p4\_UID”。在Hologres中，请前往[用户页面](https://ram.console.aliyun.com/users)获取UID账号。更多RAM账号表达格式请参见[账号概述](/intl.zh-CN/账号与权限管理/账号概述.md)。
5.  授权新用户。

    成功将新用户创建至实例后，必须在对应的DB内将新用户加入相应的用户组，以完成授权操作。如果是在创建用户的同时已经加入对应的用户组则不需要再次授权。

    如下命令中，\{dbname\}.\[admin\|\{schemaname\}.developer\|\{schemaname\}.writer\|\{schemaname\}.viewer\]是将用户加入当前DB中可供选择的用户组名称，更多描述请参见[用户组说明](/intl.zh-CN/账号与权限管理/Hologres权限模型/基于Schema级别的简单权限模型/基于Schema级别的简单权限模型概述.md)。

    ```
    call slpm_grant ('{dbname}.[admin|{schemaname}.developer|{schemaname}.writer|{schemaname}.viewer]', '云账号id/云邮箱/RAM账号'); // 将某个用户加入某个用户组
    ```

    您可以参照如下示例，将用户加入不同权限的用户组。

    ```
    // 加入某个DB的admin用户组
    call slpm_grant ('mydb.admin', '197006222995xxx', ); // 将197006222995xxx加入数据库mydb的admin用户组
    call slpm_grant ('mydb.admin', 'ALIYUN$xxx'); // 将xxx@aliyun.com加入数据库mydb的admin用户组
    
    // 加入某个DB的developer用户组
    call slpm_grant ('mydb.public.developer', '197006222995xxx'); // 将197006222995xxx加入数据库mydb的developer用户组
    call slpm_grant ('mydb.public.developer', 'RAM$mainaccount:subuser');// 将云账号mainaccount的RAM用户subuser加入数据库mydb的developer用户组
    
    // 加入某个DB的viewer用户组
    call slpm_grant ('"MYDB.lisa.viewer"', '197006222995xxx'); // 将197006222995xxx加入数据库"MYDB"的viewer用户组
    call slpm_grant ('mydb.lisa.viewer', '"xxx@aliyun.com"'); // 将账号xxx@aliyun.com加入数据库mydb的viewer用户组
    ```


## 移除用户组

如果您需要将某个用户从某个DB的某个用户组中移除，可以执行如下命令。移除之后，该用户将不具备该用户组具有的权限。

如下命令中，\{dbname\}.\[admin\|\{schemaname\}.developer\|\{schemaname\}.writer\|\{schemaname\}.viewer\]是将用户加入当前DB中可供选择的用户组名称，更多描述请参见[用户组说明](/intl.zh-CN/账号与权限管理/Hologres权限模型/基于Schema级别的简单权限模型/基于Schema级别的简单权限模型概述.md)。

```
call slpm_revoke ('{dbname}.[admin|{schemaname}.developer|{schemaname}.writer|{schemaname}.viewer]', '云账号id/云邮箱/RAM账号'); // 移除某用户的权限
```

您可以参照如下示例，将用户从不同权限的用户组移除。

```
// 将用户从某DB的admin用户组移除
call slpm_revoke ('dbname.admin', 'p4_564306222995xxx');//将564306222995xxx（RAM用户）移除admin用户组
call slpm_revoke ('dbname.admin', '197006222995xxx');//将197006222995xxx（云账号）移除admin用户组
call slpm_revoke ('dbname.admin', '"xxx@aliyun.com"');

// 将用户从某DB的developer用户组移除
call slpm_revoke ('mydb.lisa.developer', 'RAM$mainaccount:subuser'); // 将RAM用户subuser移出数据库mydb的developer用户组
call slpm_revoke ('mydb.public.developer', 'p4_564306222995xxx');//将564306222995xxx（RAM用户）移除developer用户组

// 将用户从某个DB的viewer用户组移除
call slpm_revoke ('"MYDB.SCHEMA1.viewer"', 'p4_564306222995xxx'); // 将564306222995xxx（RAM用户）移出数据库"MYDB"的viewer用户组
```

## 删除用户

您也可以根据需要对某个用户进行删除，成功删除用户后，该用户将会从当前实例删除，用户将无实例任何权限，请谨慎执行该操作。

```
DROP ROLE "云账号ID/云邮箱/RAM账号"; // 直接将该用户从实例中删除。
```

## 基于Schema级别的简单权限模型关闭

当您不需要使用基于Schema级别的简单权限模型时，您可以根据业务需求执行如下操作，关闭此功能。

1.  关闭SLPM。

    开启SLPM后，Superuser也可以根据业务需要执行如下示例内容关闭SLPM。关闭SLPM之后，对应用户组将不会被删除，对应用户组内用户拥有的权限请参见[基于Schema级别的简单权限模型函数说明](/intl.zh-CN/账号与权限管理/Hologres权限模型/基于Schema级别的简单权限模型/基于Schema级别的简单权限模型函数说明.md)。

    ```
    call slpm_disable ();
    ```

    关闭SLPM注意事项：

    -   只有Superuser可以执行关闭操作。
    -   将对PUBLIC开放public schema的USAGE和CREATE权限、DB的CONNECT和TEMPORARY权限、functions和procedures的EXECUTE权限以及language和data types \(include domains\)的USAGE权限。
    -   对于其他对象如table、view、materialized view、table column、sequence、foreign data wrapper、foreign server、schema（除public schema）等不会将权限开放给PUBLIC。如果您需要开放此权限，请联系Superuser授权。
    -   关闭SLPM之后，\{db\}.admin、\{db\}.\{schema\}.developer、\{db\}.\{schema\}.writer和\{db\}.\{schema\}.viewer将保留对当前已有对象的权限，对新建数据库对象不生效。
2.  删除用户组（通常情况下，为了方便管理不建议删除用户组）。

    关闭SLPM后，您可以根据业务需求，通过调用slpm\_cleanup函数删除用户组。场景如下：

    -   场景1：删除用户组保留DB。

        如果您需要删除DB内的用户组，但同时又希望当前DB可以继续使用，Superuser可以执行如下语句。

        ```
        call slpm_cleanup ( '<dbname>' );
        ```

        **说明：** 调用slpm\_cleanup时，请确保该DB上没有正在运行的SQL语句，否则可能会失败，并可能对服务产生影响。

        由于slpm\_cleanup需要将现有对象owner转移给当前用户，但slpm\_cleanup默认每次仅对64个（参数可调）对象转移owner。因此，您可能需要多次执行slpm\_cleanup，直到所有对象完成迁移（建议累计执行次数小于5次），并删除所有保留用户组为止。更多关于该函数的说明，请参见[slpm\_cleanup](/intl.zh-CN/账号与权限管理/Hologres权限模型/基于Schema级别的简单权限模型/基于Schema级别的简单权限模型函数说明.mdsection_aen_1tr_jzt)。

    -   场景2：先删除DB再删除用户组。

        如果您已经将原有DB删除，但用户组并未删除，Superuser可以在其他的DB（例如postgres）执行如下语句删除原有DB对应的所有用户组。

        ```
        postgres=# call slpm_cleanup ( 'mydb' );
        ```


