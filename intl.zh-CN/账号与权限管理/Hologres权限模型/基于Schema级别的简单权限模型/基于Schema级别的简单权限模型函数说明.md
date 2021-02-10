---
keyword: [Schema, 简单权限模型, 函数调用]
---

# 基于Schema级别的简单权限模型函数说明

本文为您介绍在交互式分析Hologres中基于Schema级别的简单权限模型（Schema-level Permission Model，SLPM）的函数调用说明，您可以通过调用相关函数管理Schema级别的简单权限模型。

## 函数概述

基于Schema级别的简单权限模型的函数及其功能说明如下所示。

-   [slpm\_enable](#section_kek_wve_iby)：开启基于Schema级别的简单权限模型。
-   [slpm\_migrate](#section_649_pj0_ge6)：将已有实例对象（包括表、视图及外表等）迁移至SLPM权限模型。
-   [slpm\_create\_user](#section_4s9_3o6_85l)：在SLPM中创建用户。
-   [slpm\_grant](#section_xjb_43p_lt9)：将某个用户加入到\{db\}.admin、\{db\}.\{schema\}.developer、\{db\}.\{schema\}.writer或\{db\}.\{schema\}.viewer用户组。
-   [slpm\_revoke](#section_2yh_r1n_nkk)：将某个用户从\{db\}.admin、\{db\}.\{schema\}.developer、\{db\}.\{schema\}.writer或\{db\}.\{schema\}.viewer用户组中移除。
-   [slpm\_disable](#section_lgu_f40_xx6)：为当前DB关闭SLPM。
-   [slpm\_cleanup](#section_aen_1tr_jzt)：清除DB的SLPM保留用户组，包括\{db\}.admin，\{db\}.\{schema\}.developer，\{db\}.\{schema\}.writer和\{db\}.\{schema\}.viewer。
-   [slpm\_rename\_database](#section_9ey_05j_kjn)：重命名DB。
-   [slpm\_rename\_schema](#section_8bl_t53_80u)：重命名Schema。

## slpm\_enable

-   函数介绍

    slpm\_enable\(\)函数用于开启基于Schema级别的简单权限模型。

    调用slpm\_enable\(\)函数后，系统将会自动创建\{db\}.admin、\{db\}.\{schema\}.developer、\{db\}.\{schema\}.writer和\{db\}.\{schema\}.viewer 4个用户组。

-   命令语法

    ```
    CALL slpm_enable ();
    ```

    **说明：** 仅实例的Superuser可以执行slpm\_enable\(\)函数。

-   使用说明

    调用slpm\_enable\(\)函数成功开启SLPM的使用说明如下：

    -   DB的所有权限从PUBLIC用户组收回。因此，不相关的用户将不能连接到目标DB。
    -   DB下所有Schema的所有权限从PUBLIC用户组收回。
    -   \{db\}.admin，\{db\}.\{schema\}.developer，\{db\}.\{schema\}.writer和\{db\}.\{schema\}.viewer都具有DB的连接权限。
    -   <db\>\_admin将作为DB的Owner以及DB下所有Schema的Owner。
    -   \{db\}.\{schema\}.developer，\{db\}.\{schema\}.writer和\{db\}.\{schema\}.viewer具有对应Schema的USAGE权限，\{db\}.\{schema\}.developer具有该Schema的CREATE权限。
    -   \{db\}.admin和\{db\}.\{schema\}.developer未来创建的Schema对象，Owner都是对应Schema的\{db\}.\{schema\}.developer。\{db\}.\{schema\}.writer具有读写（readwrite）权限，\{db\}.\{schema\}.viewer具有只读（readonly）权限。
    -   \{db\}.admin和\{db\}.\{schema\}.developer未来创建的无schema的对象（例如Foreign Server、FDW、Language等），Owner都是对应Schema的\{db\}.admin。\{schema\}.developer、\{db\}.\{schema\}.writer和\{db\}.\{schema\}.viewer具有使用（USAGE）权限。

## slpm\_migrate

-   函数介绍

    slpm\_migrate\(\)函数用于将已有实例对象（包括表、视图及外表等）迁移至SLPM权限模型。

-   命令语法

    ```
    CALL slpm_migrate ( [ batch_size ] );
    ```

    参数说明如下表所示。

    |参数|描述|取值范围|
    |--|--|----|
    |batch\_size|单次批量迁移对象的大小。取值为0代表采用当前[max\_locks\_per\_transaction](https://www.postgresql.org/docs/9.1/runtime-config-locks.html)作为batch\_size。

|\[1, 64\]，超出该范围则不合法。如果您的DB中有较多的对象（数百个）需要迁移，需要您多次执行slpm\_migrate\(\)函数，直到全部对象迁移完成为止。

如果您的DB内有大量表对象（超过数千甚至数万），建议您[提交工单](https://workorder-intl.console.aliyun.com/)将max\_locks\_per\_transaction参数调大，再执行迁移操作。 |

-   使用说明
    -   调用slpm\_migrate\(\)函数时，如果出现`DONE BUT NOT COMPLETED`提示，说明全部对象还没有迁移完毕，您需要继续执行slpm\_migrate\(\)迁移。
    -   如果出现`COMPLETED`提示，说明全部对象已经迁移完毕。
-   使用示例

    ```
    CALL slpm_migrate (); //迁移最多max_locks_per_transaction个对象至SLPM管理。
    
    CALL slpm_migrate (64); // 迁移64个对象至SLPM管理。
    ```


## slpm\_create\_user

-   函数介绍

    slpm\_create\_user\(\)函数用于在SLPM中创建用户。创建的用户仅具有登录权限，您需要授予其具体的权限才能进行开发。

    **说明：** 仅Superuser和\{db\}.admin的成员可以调用该函数。

-   命令语句

    ```
    CALL slpm_create_user ( user_name [, role_name] );
    ```

    参数说明如下表所示。

    |参数|描述|
    |--|--|
    |user\_name|需要创建的用户名。格式如下：    -   阿里云云账号ID，例如13432193xxxx或者xx@aliyun.com。
    -   RAM用户账号ID，例如RAM$mainaccount:subuser或者p4\_202338382183xxx。 |
    |role\_name|创建用户时可以根据业务需求选择加入如下用户组：    -   \{db\}.admin
    -   \{db\}.\{schema\}.developer
    -   \{db\}.\{schema\}.writer
    -   \{db\}.\{schema\}.viewer
关于用户组的权限说明，请参见[基于Schema级别的简单权限模型介绍](/intl.zh-CN/账号与权限管理/Hologres权限模型/基于Schema级别的简单权限模型/基于Schema级别的简单权限模型概述.md)。|

-   使用示例

    ```
    CALL slpm_create_user ('my_test@aliyun.com');
    CALL slpm_create_user ('RAM$my_test:mysubuser', 'mydb.public.developer');
    CALL slpm_create_user ('13532313103042xxx');
    CALL slpm_create_user ('p4_23319103042xxx', 'mydb.admin');
    ```


## slpm\_grant

-   函数介绍

    slpm\_grant\(\)函数用于将某个用户加入到\{db\}.admin、\{db\}.\{schema\}.developer、\{db\}.\{schema\}.writer或\{db\}.\{schema\}.viewer用户组。

    **说明：** 仅Superuser和\{db\}.admin可以调用该函数。

-   命令语法

    ```
    CALL slpm_grant ( role_name, user_name );
    ```

    参数说明如下表所示。

    |参数|描述|
    |--|--|
    |user\_name|需要授权的用户名。格式如下：    -   阿里云云账号，例如13432193xxxx或者xx@aliyun.com。
    -   RAM用户账号，例如RAM$mainaccount:subuser或者p4\_202338382183xxx。 |
    |role\_name|可以根据业务需求选择加入如下用户组：    -   \{db\}.admin
    -   \{db\}.\{schema\}.developer
    -   \{db\}.\{schema\}.writer
    -   \{db\}.\{schema\}.viewer
关于用户组的权限说明，请参见[基于Schema级别的简单权限模型介绍](/intl.zh-CN/账号与权限管理/Hologres权限模型/基于Schema级别的简单权限模型/基于Schema级别的简单权限模型概述.md)。|

-   使用说明
    -   只有开启SLPM后才能调用该函数。
    -   user\_name必须是云账号ID或者云账号。
    -   role\_name必须是\{db\}.admin、\{db\}.\{schema\}.developer、\{db\}.\{schema\}.writer或\{db\}.\{schema\}.viewer用户组。
-   使用示例

    ```
    CALL slpm_grant ('mydb.public.developer', 'p4_202338382183xxx');//授予RAM用户mydb的developer权限。
    CALL slpm_grant ('mydb.admin', 'RAM$my_test:xxx');//授予RAM用户mydb的admin权限。
    CALL slpm_grant ('otherdb.admin', '13532313103042xxx'); // 错误，加入其他DB的角色请连接到其他DB执行slpm_grant函数。
    ```


## slpm\_revoke

-   函数介绍

    slpm\_revoke\(\)函数用于将某个用户从\{db\}.admin、\{db\}.\{schema\}.developer、\{db\}.\{schema\}.writer或\{db\}.\{schema\}.viewer用户组中移除。

    **说明：** 仅Superuser和\{db\}.admin可以调用该函数。

-   命令语法

    ```
    CALL slpm_revoke ( role_name, user_name );
    ```

-   使用说明
    -   只有开启SLPM后才能调用该函数。
    -   user\_name必须是云账号ID或者云账号。
    -   role\_name必须是\{db\}.admin、\{db\}.\{schema\}.developer、\{db\}.\{schema\}.writer或\{db\}.\{schema\}.viewer用户组。
-   使用示例

    ```
    CALL slpm_revoke ('mydb.public.developer', 'p4_202338382183xxx');//将RAM用户从mydb的developer用户组移除。
    CALL slpm_revoke ('mydb.admin', 'RAM$my_test:xxx');//将RAM用户从mydb的admin用户组移除。
    CALL slpm_revoke ('otherdb.admin', '13532313103042xxx'); // 错误，移出其他DB的用户组请连接到其他DB执行slpm_grant。
    ```


## slpm\_disable

-   函数介绍

    slpm\_disable\(\)函数用于为当前DB关闭SLPM。

    **说明：** 仅Superuser可以调用该函数。

-   命令语句

    ```
    CALL slpm_disable ();
    ```

-   使用说明

    关闭slpm的说明如下：

    -   \{db\}.admin、\{db\}.\{schema\}.developer、\{db\}.\{schema\}.writer和\{db\}.\{schema\}.viewer用户组继续保留，可以使用专家模式授权语句将用户加入对用户组来做权限管理，数据库对象Owner保持不变（仍然是\{db\}.\{schema\}.devleoper）。
    -   DB的CONNECT，TEMPORARY权限向PUBLIC开放。
    -   DB中public schema的USAGE，CREATE权限向PUBLIC开放。
    -   DB中public schema中function，procedure的EXECUTE权限向PUBLIC开放。
    -   用户定义的LANGUAGE的USAGE权限向PUBLIC开放。
    -   用户定义的TYPE的USAGE权限向PUBLIC开放。
-   使用示例

    ```
    CALL slpm_disable ();
    ```


## slpm\_cleanup

-   函数介绍

    slpm\_cleanup\(\)函数用于清除DB的SLPM保留用户组，包括\{db\}.admin、\{db\}.\{schema\}.developer、\{db\}.\{schema\}.writer和\{db\}.\{schema\}.viewer。

    **说明：** 仅Superuser可以调用此函数。

-   命令语句

    ```
    CALL slpm_cleanup ( db_name [, batch_size ] );
    ```

    参数说明如下表所示。

    |参数|描述|取值范围|
    |--|--|----|
    |db\_name|需要清除用户组的DB。|如果名称中包含特殊字符或大写字母的db\_name用双引号（""）引起来。例如`“MYDB”`。|
    |batch\_size|单次批量迁移对象的大小。取值为0代表默认采用\[0,64\][max\_locks\_per\_transaction](https://www.postgresql.org/docs/9.1/runtime-config-locks.html)作为当前batch\_size。

|\[0, 64\]，超出该范围则不合法。如果您的DB中有大量的对象（数百个）需要迁移，可能会导致锁溢出，需要您多次执行slpm\_migrate\(\)函数，直到全部对象迁移完成为止。

如果您的DB中有大量的对象（超过数千甚至数万）需要迁移，建议您[提交工单](https://workorder-intl.console.aliyun.com/)将batch\_size参数调大，再执行迁移操作。 |

-   使用说明
    -   调用slpm\_cleanup\(\)函数时，如果出现`DONE BUT NOT COMPLETED`提示，说明全部对象还没有迁移完毕，并且未删除保留用户组，您需要继续执行slpm\_cleanup\(\)函数。
    -   如果出现`COMPLETED`提示，说明全部对象已经迁移完毕，并已删除保留用户组，无需再调用此函数。
-   使用示例

    ```
    CALL slpm_cleanup ('mydb'); //单次最多将max_locks_per_transaction个对象转移Owner到current_user。
    CALL slpm_cleanup ('mydb', 64);//单次转移64个对象的Owner到current_user。
    ```

    -   场景1：删除对应DB，再清除用户组。

        ```
        drop database mydb;
        CALL slpm_cleanup ('mydb');  // 无需重试，一次成功。
        ```

    -   场景2：在DB仍然存在的情况下，需要连接到对应DB进行清除。

        ```
         CALL slpm_cleanup ('otherdb');
        ERROR:  Permission Denied. execute in database otherdb, or drop database before call slpm_cleanup.
        ```


## slpm\_rename\_database

-   函数介绍

    slpm\_rename\_database\(\)函数用于重命名DB。

    开启SLPM时，重命名DB需要同步修改4个用户组的命名。因此，需要调用slpm\_rename\_database\(\)函数来重命名DB。

    **说明：** 仅Superuser和\{db\}.admin成员可以调用该函数。

-   命令语法

    ```
    CALL slpm_rename_database ( old_name, new_name );
    ```

    参数说明如下表所示。

    |参数|描述|
    |--|--|
    |old\_name|需要重命名的目标DB名。包含特殊字符或大写字母的db\_name需要使用双引号（""）引起来。例如`“MYDB”`。|
    |new\_name|将DB重命名为new\_name。包含特殊字符或大写字母的db\_name需要使用双引号（""）引起来。例如`“MYDB”`。|

-   使用示例

    ```
    CALL slpm_rename_database ('olddb', 'newdb');  
    ```


## slpm\_rename\_schema

-   函数介绍

    slpm\_rename\_schema\(\)函数用于重命名Schema。

    开启SLPM时，重命名DB需要同步修改4个用户组的命名。因此，需要调用slpm\_rename\_schema\(\)函数来重命名DB。

    **说明：** 仅Superuser和\{db\}.admin成员可以调用该函数。

-   命令语法

    ```
    CALL slpm_rename_schema ( old_name, new_name );
    ```

    参数说明如下表所示。

    |参数|描述|
    |--|--|
    |old\_name|需要重命名的目标Schema名。包含特殊字符或大写字母的schema\_name需要使用双引号（""）引起来。例如`“MYDB”`。|
    |new\_name|将Schema重命名为new\_name。包含特殊字符或大写字母的schema\_name需要使用双引号（""）引起来。例如`“MYDB”`。|

-   使用示例

    ```
    CALL slpm_rename_schema ('oldschema', 'newschema');  
    ```


