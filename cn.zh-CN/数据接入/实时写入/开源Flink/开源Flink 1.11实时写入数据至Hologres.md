---
keyword: [开源Flink, 实时导入数据]
---

# 开源Flink 1.11实时写入数据至Hologres

本文为您介绍开源Flink 1.11如何实时写入数据至Hologres。

## 前提条件

-   开通Hologres实例，并连接开发工具。您可以使用Holoweb连接Hologres，详情请参见[HoloWeb快速入门](/cn.zh-CN/快速入门/HoloWeb快速入门.md)。
-   创建Flink集群。您可以进入Flink官网下载二进制文件，启动一个Standalone集群，详细操作步骤请参见[集群搭建](https://ci.apache.org/projects/flink/flink-docs-release-1.11/try-flink/local_installation.html)。
-   单击下载[Hologres Connector依赖的JAR文件](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/207600/cn_zh/1615792181869/ververica-connector-hologres-1.11-vvr-2.1-SNAPSHOT-jar-with-dependencies.jar)并安装至本地maven库。

    ```
    mvn install:install-file -Dfile=ververica-connector-hologres-1.11-SNAPSHOT-jar-with-dependencies.jar -DgroupId=com.alibaba.ververica -DartifactId=ververica-connector-hologres -Dversion=1.11-SNAPSHOT -Dpackaging=jar -DgeneratePom=true --classifier=jar-with-dependencies
    ```

-   进入Hologres的[Git官方示例库](https://github.com/hologres/hologres-flink-examples/tree/flink1.11)，下载并编译JAR文件。

## 使用说明

您可以将Flink 1.11版本的数据写入Hologres的源表、维表和结果表，具体内容如下：

|类型|说明|
|--|--|
|Hologres源表|写Hologres的源表用法同Flink全托管，详情请参见[Hologres源表](/cn.zh-CN/数据接入/实时写入/实时计算Flink版/Flink全托管/Hologres源表.md)。|
|Hologres维表|写Hologres的维表用法同Flink全托管，详情请参见[Hologres维表](/cn.zh-CN/数据接入/实时写入/实时计算Flink版/Flink全托管/Hologres维表.md)。|
|Hologres结果表|写Hologres的结果表用法同Flink全托管，详情请参见[Hologres结果表](/cn.zh-CN/数据接入/实时写入/实时计算Flink版/Flink全托管/Hologres结果表.md)。|

