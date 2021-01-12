---
keyword: [Hologres, 权限模型]
---

# Hologres权限模型概述

当一个用户登录Hologres实例后，必须有实例相关的操作权限才能在权限范围内进行操作。本文为您介绍使用Hologres实例开发需要的权限。

## Hologres鉴权流程

一个用户从连接Hologres到使用Hologres的完整鉴权流程如下：

![Hologres鉴权流程](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7398039061/p207367.png)

## 用户概念

当一个阿里云账号连接Hologres时，需要被创建成为Hologres的用户（管理员需要执行`cretae user "xxx"`，否则会报错`role "xxx" does not exist`），才能连接成功。用户属于实例级别，所以添加用户和删除用户相当于把用户创建进实例中或者从实例中删除。在实例内进行的具体操作（例如创建表等），则是DB级别的权限，授予权限时需要在某一个DB中执行，只享有这个DB内对象的权限，不能跨DB使用权限。

## 权限模型

Hologres兼容PostgreSQL 11，为用户授予实例开发权限时，可以使用标准的PostgreSQL授权语句（专家权限模型），由于PostgresQL的权限粒度较细，且授权语句比较复杂难懂，Hologres在此基础上又提供简单权限模型，提供更加便捷的权限控制。

关于简单权限模型和专家权限模型的区别和使用场景如下：

|权限类型|适用场景|说明|
|----|----|--|
|专家权限模型（PostgreSQL）|适用于需要非常严格权限管控的场景。例如，精确到某个人用某个表。|专家权限模型的权限授予粒度小且灵活，可以为用户授予具体某个表的权限，但是授权语法比较复杂。具体权限授予操作，请参见[专家权限模型](/cn.zh-CN/账号与权限管理/Hologres权限模型/专家权限模型.md)。|
|简单权限模型（SPM）|DB级别的权限管控，适用于粗粒度的权限管理场景。|简单权限模型是封装好的权限模型，以DB为维度，每个用户组都有对应的权限，不可修改，能满足大部分授权场景，且授权操作比较简单。具体权限授予操作，请参见[简单权限模型](/cn.zh-CN/账号与权限管理/Hologres权限模型/简单权限模型/简单权限模型的使用.md)。|
|Schema级别的简单权限模型（SLPM）|精确到Schema级别的权限管控，使用于对权限粒度较为细致且又希望简化授权流程的场景。|Schema级别的简单权限模型是已经封装好的权限模型，以Schema为维度，每个用户组都有对应的权限，不可修改，满足对于权限较为细粒度的管控，且授权操作比较简单。具体权限授予操作，请参见[基于Schema级别的简单权限模型的使用](/cn.zh-CN/账号与权限管理/Hologres权限模型/基于Schema级别的简单权限模型/基于Schema级别的简单权限模型的使用.md)。|

## 权限授予

用户想要使用Hologres进行开发，需要授予具体的权限才能在权限范围内操作，专家权限模型和简单权限模型授权的具体操作和权限关系如下两个表格所示，您可以根据需要的操作配合所使用的权限模型来为用户授权。

**说明：** Superuser默认具有表格中的所有权限。

|专家权限模型操作|需要的权限|授权语句|
|--------|-----|----|
|CREATE USER\(ROLE\)

DROP USER\(ROLE\)

|CREATEROLE|如下示例为授予用户A创建角色的权限。```
alter user A createrole;
``` |
|CREATE TABLE

VIEW TABLE

FOREIGN TABLE

|DB的CONNECT权限以及对应Schema的CREATE权限|如下示例为授权用户A有xx schema的建表权限。```
GRANT CREATE ON SCHEMA xx TO A;
```

任何用户默认有public schema下的建表权限。|
|SELECT|对应Schema的USAGE权限以及SELECT权限|您可以按照如下方式进行授权：```
GRANT USAGE ON SCHEMA xx to A;
GRANT SELECT ON TABLE xx TO A;
GRANT SELECT ON ALL TABLES IN SCHEMA xx TO A;
``` |
|INSERT

UPDATE

DELETE

TRUNCATE

|对应Schema的USAGE权限以及INSERT/UPDATE /DELETE /TRUNCATE权限|您可以按照如下方式进行授权：```
GRANT USAGE ON SCHEMA xx to A;
GRANT 【INSERT/UPDATE/DELETE/TRUNCATE】 ON TABLE xx TO A;
GRANT 【INSERT/UPDATE/DELETE/TRUNCATE】 ON ALL TABLES IN SCHEMA xx TO A;
``` |
|ALTER TABLE|表的owner（表的owner可以用ALTER OWNER改变）|删除表不能使用GRANT，只能执行命令`ALTER TABLE xx OWNER TO A;`将表的owner修改为A后，由A删除该表。|
|DROP TABLE|
|CREATE DATABASE|CREATEDB|如下示例为授予用户A创建数据库的权限：```
ALTER USER A CREATEDB;
``` |
|DROP DATABASE|DB owner|删除数据库不能使用GRANT，只能执行命令`ALTER DATABASE xx OWNER TO A;`将数据库的owner修改为A后，由A删除该DB。|
|CREATE EXTENSION|DB owner|-|
|GRANT

REVOKE

|具有相应权限，并具备GRANT OPTION的用户|如下示例为授予用户GRANT操作权限：```
GRANT 【Privilege】TO A WITH GRANT OPTION;
``` |

|权限模式授权|简单权限模型（SPM）|Schema级别的简单权限模型（SLPM）|
|------|-----------|---------------------|
|操作|需要的权限|授权语句|需要的权限|授权语句|
|CREATE USER\(ROLE\)

DROP USER\(ROLE\)

|DB admin|您可以选择如下两种方式为用户授予admin权限：-   ```
call spm_grant('A', '<dbname>_admin');
```

-   ```
call spm_create_user('A', '<dbname>_admin');
```


|DB admin|您可以选择如下两种方式为用户授予admin权限：-   ```
call slpm_create_user('A', '<dbname>.<schema>.admin');
```

-   ```
call slpm_grant('A', '<dbname>.<schema>.admin');
``` |
|CREATE TABLE

VIEW TABLE

FOREIGN TABLE

|拥有superuser、admin或者developer用户组权限|您可以参照本表格内SPM的授权语句为用户授予admin或者developer权限。|拥有superuser、schema的admin或者developer用户组权限|您可以参照本表格内SLPM的授权语句为用户授予schema的admin或者developer权限。|
|SELECT|拥有superuser、admin、developer、writer或viewer用户组权限|您可以参照本表格内SPM的授权语句为用户授予admin或者developer、writer、viewer权限。|拥有superuser、schema的admin、developerwriter或viewer用户组权限|您可以参照本表格内SLPM的授权语句为用户授予schema的admin、developer、writer或viewer权限。|
|INSERT

UPDATE

DELETE

TRUNCATE

|拥有superuser、admin、developer或writer用户组权限|您可以参照本表格内SPM的授权语句为用户授予admin、developer或者writer权限。|拥有superuser、schema的admin、developer或writer用户组权限|您可以参照本表格内SLPM的授权语句为用户授予schema的admin、developer、writer或viewer权限。|
|ALTER TABLE|拥有superuser、admin或者developer用户组权限|您可以参照本表格内SPM的授权语句为用户授予admin或者developer权限。|拥有superuser、schema的admin或者developer用户组权限|您可以参照本表格内SLPM的授权语句为用户授予schema的admin或者developer权限。|
|DROP TABLE|
|CREATE DATABASE

DROP DATABASE

CREATE EXTENSION

|DB admin|您可以参照本表格内SPM的授权语句为用户授予admin权限。|DB admin|您可以参照本表格内SLPM的授权语句为用户授予schema的admin权限。|
|GRANT

REVOKE

|DB admin|如下示例分别为用户A授予GRANT和REVOKE权限：```
call spm_grant('A', 'role');
call spm_revoke('A', 'role');
```

|DB admin|如下示例分别为用户A授予GRANT和REVOKE权限：```
call slpm_grant('A', 'role');
call slpm_revoke('A', 'role');
``` |

