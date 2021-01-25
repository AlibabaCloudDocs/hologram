---
keyword: [SQL命令, Hologres, 导出数据至OSS]
---

# 使用SQL命令导出Hologres的数据至OSS

阿里云对象存储（Object Storage Service，简称 OSS）是阿里云提供的安全、低成本及高可靠的云存储服务。本文为您介绍如何使用SQL命令导出Hologres中的数据至OSS。

## 使用限制

仅当前Hologres实例的Superuser或拥有`pg_execute_server_program`权限的用户，才可以导出Hologres的数据至OSS。Superuser可以授予其他用户`pg_execute_server_program`权限，命令如下。

```
GRANT pg_execute_server_program TO "云账号ID/云账号";
```

单次导入至OSS的数据量不能超过5G。

## 语法

`DUMP TO OSS`命令用于导出Hologres的查询结果至指定的OSS，格式如下。

```
COPY (query) TO PROGRAM 'hg_dump_to_oss --AccessKeyId dummy_id --AccessKeySecret dummy_key --Endpoint dummy_host --BucketName dummy_bucket --DirName dummy_dir --BatchSize 5000 ' DELIMITER ',';
```

参数说明如下表所示。

|参数|描述|是否为必选参数|示例|
|--|--|-------|--|
|query|输入的查询语句。|是|`SELECT * FROM dual;`|
|AccessKeyId|当前账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。

|是|无|
|AccessKeySecret|当前账号的AccessKey SECRET。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。

|是|无|
|Endpoint|OSS的经典网络访问域名。您可以单击[Bucket列表](https://oss.console.aliyun.com/bucket)页面的目标Bucket名称，进入Bucket详情页查看。

|是|oss-cn-beijing-internal.aliyuncs.com|
|BucketName|OSS对应的Bucket名称。|是|dummy\_bucket|
|DirName|OSS存放输出结果的目录。|是|testdemo/|
|BatchSize|每次执行`DUMP TO OSS`的行数，默认为1000。|否|5000|
|DELIMITER|结果列之间的分隔符，默认为制表符（Tab-separated Values，简称TSV）。|否|','|

## 使用示例

`DUMP TO OSS`命令的使用示例如下。

```
//将交互式分析内部表数据dump到指定OSS
COPY (select * from test LIMIT 2) TO PROGRAM 'hg_dump_to_oss --AccessKeyId <access id> --AccessKeySecret <access key> --Endpoint oss-cn-hangzhou-internal.aliyuncs.com --BucketName hologres-demo --DirName holotest/ --BatchSize 3000' DELIMITER ',';

//将交互式分析外表数据dump到指定OSS
COPY (select * from bank_data LIMIT 20) TO PROGRAM 'hg_dump_to_oss --AccessKeyId <access id> --AccessKeySecret <access key> --Endpoint oss-cn-hangzhou-internal.aliyuncs.com --BucketName hologres-demo --DirName holotest/ --BatchSize 3000' DELIMITER ',';

//跨region dump到指定OSS
COPY (select * from bank_data LIMIT 20) TO PROGRAM 'hg_dump_to_oss --AccessKeyId <access id> --AccessKeySecret <access key> --Endpoint oss-cn-beijing-internal.aliyuncs.com --BucketName hologres-demo --DirName holotest/ --BatchSize 3000' DELIMITER ',';
```

**说明：** Hologres支持跨地域导出数据至指定的OSS。例如，可以导出杭州地域的实例查询出的数据至北京地域的OSS。

## 常见问题

常见的报错内容及解决方法如下：

-   `ERROR: syntax error at or near ")"LINE 1: COPY (select 1,2,3 from ) TO PROGRAM 'hg_dump_to_oss2 --Acce...`

    输入的query有误，请检查对应的查询语句。

-   `DETAIL: child process exited with exit code 255`

    选择的OSS网络类型有误。如果您使用的是公共云，请选择**经典网络**。

-   `DETAIL: command not found`

    您需要配置`DUMP TO OSS`的**prpgram**为hg\_dump\_to\_oss，否则会出现该报错。

-   `ERROR: program "hg_dump_to_oss ...” failed DETAIL: child process exited with exit code 101`

    输入的AccessKeyId不合法，请使用当前账号的AccessKey ID。

-   `ERROR: program "hg_dump_to_oss ...” failed DETAIL: child process exited with exit code 102`

    输入的AccessKeySecret不合法，请使用当前账号的AccessKey Secret。

-   `ERROR: program "hg_dump_to_oss ...” failed DETAIL: child process exited with exit code 103`

    输入的Endpoint不合法，请确认对应OSS经典网络的Endpoint。

-   `ERROR: program "hg_dump_to_oss ...” failed DETAIL: child process exited with exit code 104`

    输入的BucketName不合法，请确认对应的Bucket名称。

-   `ERROR: program "hg_dump_to_oss ..." failed DETAIL: child process exited with exit code 105`

    缺少参数，请对照参数说明，检查必选参数是否均已配置。


