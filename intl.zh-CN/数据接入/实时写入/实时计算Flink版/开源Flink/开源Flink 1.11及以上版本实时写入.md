---
keyword: [开源Flink, 实时导入数据]
---

# 开源Flink 1.11及以上版本实时写入

本文为您介绍开源Flink 1.11如何实时写入数据至Hologres。

## 背景信息

从开源Flink1.11版本开始，Hologres代码已开源。详细内容请参见[Hologres GitHub官方库](https://github.com/aliyun/alibabacloud-hologres-connectors)。

## Flink SQL写入数据至Hologres代码示例

您可以参照如下代码示例，通过将Flink SQL将数据写入Hologres。其中，更多详细的代码示例请参见[Hologres GitHub官方库](https://github.com/aliyun/alibabacloud-hologres-connectors/tree/master/hologres-connector-example/src/main/java/com/alibaba/ververica/connectors/hologres/example)。

```
        String createHologresTable =
                String.format(
                        "create table sink("
                                + "  user_id bigint,"
                                + "  user_name string,"
                                + "  price decimal(38,2),"
                                + "  sale_timestamp timestamp"
                                + ") with ("
                                + "  'connector'='hologres',"
                                + "  'dbname' = '%s',"
                                + "  'tablename' = '%s',"
                                + "  'username' = '%s',"
                                + "  'password' = '%s',"
                                + "  'endpoint' = '%s'"
                                + ")",
                        database, tableName, userName, password, endPoint);
        tEnv.executeSql(createHologresTable);

        createScanTable(tEnv);

        tEnv.executeSql("insert into sink select * from source");
```

## Hologres Flink Connector参数说明

您可以将Flink数据写入Hologres，Hologres Flink Connector相关参数具体内容如下：

|参数|是否必填|说明|
|--|----|--|
|connector|是|结果表类型，固定值为hologres。|
|dbname|是|Hologres的数据库名称。|
|tablename|是|Hologres接收数据的表名称。|
|username|是|当前阿里云账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。 |
|password|是|当前阿里云账号的AccessKey Secret。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。 |
|endpoint|是|Hologres的VPC网络地址。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取Endpoint。**说明：** endpoint需包含端口号，格式为`ip:port`同一个区域使用VPC网络地址，跨区域请使用公共网络。 |
|mutatetype|否|数据写入模式，默认值为`insertorignore`。|
|ignoredelete|否|是否忽略撤回消息。通常Flink的Group by会产生回撤消息，回撤消息到Hologres Connector会产生Delete请求。默认值为`false`**说明：** 仅在使用流式语义时生效。 |
|partitionrouter|否|是否写入分区表。默认值为`false`。|
|createparttable|否|当写入分区表时，是否自动根据分区值自动创建分区表。具体取值如下：-   false（默认值）：不会自动创建分区表。
-   true：自动创建分区表。

**说明：** Flink 全托管2.1.x版本及以上支持自动创建分区表。建议您在使用该功能之前，确保分区值不会出现脏数据，否则会导致分区表创建错误。 |
|connectionSize|否|单个Flink Hologres Task所创建的JDBC连接池大小。默认值为3，和吞吐成正比。|
|jdbcWriteBatchSize|否|Hologres Sink节点数据攒批的最大批大小（JDBC模式）。默认值为256。|
|jdbcWriteFlushInterval|否|Hologres Sink节点数据攒批的最长Flush等待时间（JDBC模式）默认值为10000ms，即10秒。|

