---
keyword: [Spark, Hologres, JDBC, 导入数据]
---

# 导入Spark的数据至Hologres

本文为您介绍如何通过Spark Connector及JDBC导入Spark中的数据至Hologres。

Spark是处理大规模数据的统一分析引擎。Hologres与Spark深度连通，内置Spark Connector，支持使用流方式（Stream）或批方式（Batch）导入Spark的数据至Hologres，帮助您快速搭建数据仓库。

**说明：** Hologres支持的Spark类型包括开源Spark和EMR Spark。

导入Spark数据的方式如下：

-   [通过Spark Connector导入数据至Hologres](#section_n2p_xfl_l4v)。
-   [通过JDBC导入数据至Hologres](#section_ird_ayu_ypi)。

## 通过Spark Connector导入数据至Hologres

Hologres支持使用内置的Spark Connector将Spark数据写入Hologres，相比其他写入方式，调用Connector的方式性能更优，推荐您使用。具体操作步骤如下：

1.  获取JAR文件。

    单击​[Spark JAR](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/207600/cn_zh/1616050136118/emr-datahub_shaded_2.11-2.2.0-SNAPSHOT.jar)，下载Hologres已自动生成的JAR文件。您可以在[Git库](https://github.com/aliyun/aliyun-emapreduce-datasources)中查看JAR文件的源代码。

2.  使用JAR文件。

    执行如下命令使用JAR文件：

    -   EMR Spark

        ```
        spark-shell --jars emr-datahub_shaded_2.11-2.0.0-SNAPSHOT.jar --driver-class-path emr-datahub_shaded_2.11-2.0.0-SNAPSHOT.jar
        ```

    -   开源Spark

        ```
        spark-shell --jars emr-datahub_shaded_2.11-2.0.0-SNAPSHOT.jar
        ```

3.  获取Hologres的Endpoint。

    通过Spark Connector导入数据需要使用Hologres实时数据API的Endpoint。在Hologres中获取实时数据API的Endpoint，命令如下。

    ```
    show hg_datahub_endpoints;
    ```

    执行完上述命令后，会显示3种网络类型的Endpoint，您可以根据自己的网络环境选择合适的Endpoint，如下图所示。

    ![实时数据API显示结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6068955061/p181724.png)

    -   `intranet,xxxx-hh-cn-hangzhou-internal.hologres.aliyuncs.com:80`表示内部网络的Endpoint。
    -   `vpc,xxxx-hh-cn-hangzhou-vpc.hologres.aliyuncs.com:80`表示VPC网络的Endpoint。
    -   `internet,xxxx-hh-cn-hangzhou.hologres.aliyuncs.com:80`表示公共网络的Endpoint。
4.  在Spark中连接Hologres，配置信息如下。

    ```
    df.write
      .format("datahub")//必须配置为DataHub。
      .option("access.key.id", "AccessKey ID")//当前阿里云账号的AccessKey ID。
      .option("access.key.secret", "AccessKey Secret")//当前阿里云账号的AccessKey Secret。
      .option("endpoint", "HolohubEndpoint")//当前Hologres实时数据API的Endpoint，即从步骤3的执行结果中获取的Endpoint。
      .option("project", "Database")//当前Hologres的数据库名称。
      .option("topic, "Table") //当前Hologres用于接收数据的表名称。
      .option("batch.size", "100")//配置批量导入数据的条数，此处示例配置为100条。
      .save()
    ```

    **说明：** batch.size参数表示批量导入数据的条数，默认为1。请您根据实际业务合理设置该值。


**使用示例**

1.  Hologres创建表。

    在Hologres中创建一张用于接受数据的表，表的字段类型与Spark表的字段类型一一映射。示例建表语句如下。

    ```
    BEGIN;
    CREATE TABLE tb001 (
      product_id BIGINT,
      product_name TEXT,
      price NUMERIC(38, 18),
      out_of_stock bool,
      weight double precision,
      ts timestamptz
    );
    COMMIT;
    ```

2.  准备Spark数据，并导入至Hologres。

    在Spark中准备一张表并写入数据。示例为在Spark Shell中使用JAR文件导入数据至Hologres，语句如下。

    ```
    //准备数据。
    val data = Seq(
          Row(1L, "iphone", 1234.56, false, 123.45, "2020-05-21 00:00:00"),
          Row(2L, "Huawei", 1234.56, true, 123.45, "2020-05-21 00:00:00")
    )
    
    
    val schema = StructType(Array(
      StructField("product_id", LongType),
      StructField("product_name", StringType),
      StructField("price", DecimalType),
      StructField("out_of_stock", BooleanType),
      StructField("weight", DoubleType),
      StructField("ts", TimestampType)
    ))
    
    val df = spark.createDataFrame(
      spark.sparkContext.parallelize(data),
      schema
    )
    //配置导入数据至Hologres的信息。
    df.write
      .format("datahub")
      .option(DatahubSourceProvider.OPTION_KEY_ACCESS_ID, 'AccessKey ID')//阿里云账号的AccessKey ID。
      .option(DatahubSourceProvider.OPTION_KEY_ACCESS_KEY, 'Accesskey Secret')//阿里云账号的Accesskey Secret。
      .option(DatahubSourceProvider.OPTION_KEY_ENDPOINT, 'endpoint:port')//Hologres实时数据API的Endpoint和Port。
      .option(DatahubSourceProvider.OPTION_KEY_PROJECT, 'database')//Hologres的数据库名称。
      .option(DatahubSourceProvider.OPTION_KEY_TOPIC, 'tb001')//Hologres用于接收数据的表名称，示例接收数据的表为tb001。
      .option("decimal.precision", 38)
      .option("decimal.scale", 18)
      .option("batch.size", "100")//配置批量导入数据的条数，此处示例配置为100条。
      .save()
    ```

    如果Spark数据中有DECIMAL类型，则需要使用如下语句设置该数据的精度。

    ```
    df.option("decimal.precision", 38)
      .option("decimal.scale", 18)
    ```


## 通过JDBC导入数据至Hologres

Hologres兼容PostgreSQL生态，提供JDBC/ODBC Driver，因此Spark也可以通过JDBC的方式写入数据至Hologres。

使用JDBC方式导入数据之前，您需要前往官网下载[PostgresSQL JDBC JAR](https://jdbc.postgresql.org/download.html)文件（请下载42.2.18及以上版本），在Spark Shell中执行如下命令启动该JAR。

```
./bin/spark-shell --jars /path/to/postgresql-42.2.18.jar
```

使用JDBC导入数据分为Batch方式和Stream方式，具体如下：

-   Batch方式

    在Spark中准备数据并配置批量导入数据至Hologres的信息。语句如下。

    ```
    //准备数据。
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.Row
    
    val data = Seq(
        Row(1L, "test"),
        Row(2L, "test2"),
        Row(3L, "test2"),
        Row(4L, "test2"),
        Row(5L, "test2"),
        Row(6L, "test2")
    )
    val schema = StructType(Array(
     StructField("a", LongType),
     StructField("b", StringType)
    ))
    
    val df = spark.createDataFrame(spark.sparkContext.parallelize(data),schema)
    
    // 配置导入数据的信息。
    .option("checkpointLocation", checkpointLocation)
    df.write.format("jdbc")
      .option("url", "jdbc:postgresql://endpoint:port/database")
      .option("driver", "org.postgresql.Driver")
      .option("dbtable", "table")//Hologres用于接收数据的表名称。
      .option("user", "accesskey id")//当前阿里云账号的AccessKey ID。
      .option("password", "accesskey secret")//当前阿里云账号的Accesskey Secret。
      .option("isolationLevel", "NONE")
      .save()
    ```

    参数说明如下表所示。

    |参数|描述|
    |--|--|
    |`jdbc:postgresql://endpoint:port/database`|    -   **endpoint**：Hologres实例的网络地址。
    -   **port**：Hologres实例的端口。
    -   **database**：Hologres的数据库名称。
您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取实例地址和端口，从**DB管理**获取数据库名称。|
    |table|Hologres用于接收数据的表名称。|
    |accesskey id|当前阿里云账号的AccessKey ID。您可以单击[用户信息管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。 |
    |accesskey secret|当前阿里云账号的AccessKey Secret。您可以单击[用户信息管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。 |

-   Stream方式
    1.  生成JDBC JAR文件。

        开源Spark或EMR Spark均可以使用如下命令获取JDBC Driver，并编译生成`emr-jdbc_2.11-2.1.0-SNAPSHOT.jar`文件。

        ```
        ./bin/spark-shell --jars /path_to/emr-jdbc_2.11-2.1.0-SNAPSHOT.jar,postgresql-42.2.18.jar --driver-class-path /path_to/emr-jdbc_2.11-2.1.0-SNAPSHOT.jar,postgresql-42.2.18.jar
        ```

    2.  在Spark Shell中执行如下命令使用JDBC JAR文件。

        ```
        ./bin/spark-shell --jars /path_to/emr-jdbc_2.11-2.1.0-SNAPSHOT.jar,postgresql-42.2.18.jar --driver-class-path /path_to/emr-jdbc_2.11-2.1.0-SNAPSHOT.jar,postgresql-42.2.18.jar
        ```

    3.  准备Spark数据并导入至Hologres。

        在Spark中创建一张表并写入数据。配置导入数据至Hologres的信息，并使用流方式实时导入数据至Hologres。

        ```
        val wordCounts = lines.flatMap(_.split(" ")).groupBy("value").count()
        
        val query = wordCounts.writeStream
          .outputMode("complete")
          .format("jdbc2")
          .option("url", "jdbc:postgresql://endpoint:port/database")//配置为相应Hologres的实例信息。
          .option("driver", "org.postgresql.Driver")
          .option("dbtable", "table")//Hologres用于接收数据的表名称。
          .option("user", "accesskey id")//当前阿里云账号的AccessKey ID。
          .option("password", "accesskey secret")//当前阿里云账号的AccessKey Secret。
          .option("batchsize", 100) //配置批量导入数据的条数，此处示例配置为100条。
          .option("isolationLevel", "NONE")
          .option("checkpointLocation", checkpointLocation)
          .start()
        
        query.awaitTermination()
        ```

        **说明：**

        -   合理设置`batchsize`参数可以提高导入数据的性能。
        -   当**Provider**的名称配置为jdbc2时，流计算模式的**EMR-SDK**性能更好。开源Spark和EMR Spark均可以直接使用jdbc2。

## 数据类型映射

Spark和Hologres的数据类型映射如下表所示。

|Spark|Hologres|
|-----|--------|
|LongType|BIGINT|
|StringType|TEXT|
|DecimalType|NUMERIC\(38, 18\)|
|BooleanType|BOOL|
|DoubleType|DOUBLE PRECISION|
|TimestampType|TIMESTAMPTZ|

