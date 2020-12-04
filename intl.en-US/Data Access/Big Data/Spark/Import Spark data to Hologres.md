---
keyword: [Spark, Hologres, JDBC, import data]
---

# Import Spark data to Hologres

This topic describes how to use the Spark connector and the Java Database Connectivity \(JDBC\) driver to import Spark data to Hologres.

Spark is a unified analytics engine that processes large amounts of data. Hologres is integrates with Spark and provides a built-in Spark connector. You can import Spark data to Hologres in stream or batch mode to build data warehouses.

**Note:** Hologres supports Spark Community Edition and Amazon Elastic MapReduce \(EMR\) Spark.

Perform the following steps to import Spark data to Hologres:

-   [Use the Spark connector to import data to Hologres](#section_n2p_xfl_l4v).
-   [Use the JDBC driver to import data to Hologres](#section_ird_ayu_ypi).

## Use the Spark connector to import data to Hologres

We recommend that you use the built-in Spark connector to import Spark data to Hologres. Compared with other methods, the Spark connector ensures better performance of data import. To use the Spark connector, perform the following steps:

1.  Obtain a JAR file.

    Click [here](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/30259/cn_zh/1605085168372/emr-datahub_shaded_2.11-2.2.0-SNAPSHOT.jar) to download the generated JAR file. You can view the source code of the JAR file on [GitHub](https://github.com/aliyun/aliyun-emapreduce-datasources).

2.  Use the JAR file.

    Run one of the following commands to use the JAR file:

    -   EMR Spark

        ```
        spark-shell --jars emr-datahub_shaded_2.11-2.0.0-SNAPSHOT.jar --driver-class-path emr-datahub_shaded_2.11-2.0.0-SNAPSHOT.jar
        ```

    -   Spark Community Edition

        ```
        spark-shell --jars emr-datahub_shaded_2.11-2.0.0-SNAPSHOT.jar
        ```

3.  Obtain the endpoint that is used to connect to Hologres.

    If you use the Spark connector to import data, you must provide the endpoint of the HoloHub API. Run the following command to query the endpoint of the HoloHub API:

    ```
    show hg_datahub_endpoints;
    ```

    After you run the preceding command, the endpoints of the three network types are displayed. Select an endpoint based on your network environment, as shown in the following figure.

    ![Endpoints](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2396707061/p181724.png)

    -   `intranet,xxxx-hh-cn-hangzhou-internal.hologres.aliyuncs.com:80`: the endpoint of the internal network.
    -   `vpc,xxxx-hh-cn-hangzhou-vpc.hologres.aliyuncs.com:80`: the endpoint of the VPC network.
    -   `internet,xxxx-hh-cn-hangzhou.hologres.aliyuncs.com:80`: the endpoint of the Internet.
4.  Run the following commands to connect Spark to Hologres:

    ```
    df.write
      .format("datahub")// This parameter must be set to datahub.
      .option("access.key.id", "<AccessKey ID>")// The AccessKey ID of your Alibaba Cloud account.
      .option("access.key.secret", "<AccessKey SECRET>")// The AccessKey secret of your Alibaba Cloud account.
      . option("endpoint", "HolohubEndpoint")// The endpoint of the HoloHub API that is obtained in Step 3.
      .option("project", "<Database>")// The name of the Hologres database.
      .option("topic, "<Table>") // The name of the table that Hologres uses to receive data.
      .option("batch.size", "<100>")// The number of data records to be imported in each batch. In this example, the parameter is set to 100.
      .save()
    ```

    **Note:** The batch.size parameter specifies the number of data records to be imported in each batch. The default value is 1. Set this parameter based on your business requirements.


Example

1.  Create a table in Hologres.

    Create a table in Hologres to receive data. The data types of the columns in the table must be mapped to data types in Spark. The following statement provides an example to demonstrate how to create a table in Hologres:

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

2.  Prepare and import Spark data to Hologres.

    Create a table in Spark and write data to the table. In the Spark shell, use a JAR file to import data to Hologres. The following code provides an example to demonstrate how to prepare and import Spark data to Hologres:

    ```
    // Prepare data.
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
    // Set the following parameters to import the data to Hologres.
    df.write
      .format("datahub")
      .option(DatahubSourceProvider.OPTION_KEY_ACCESS_ID, '<AccessKey ID>')// The AccessKey ID of your Alibaba Cloud account.
      .option(DatahubSourceProvider.OPTION_KEY_ACCESS_KEY, '<AccessKey SECRET>')// The AccessKey secret of your Alibaba Cloud account.
      .option(DatahubSourceProvider.OPTION_KEY_ENDPOINT, '<Endpoint:Port>')// The endpoint and port number of the HoloHub API.
      .option(DatahubSourceProvider.OPTION_KEY_PROJECT, '<Database>')// The name of the Hologres database.
      .option(DatahubSourceProvider.OPTION_KEY_TOPIC, '<tb001>')// The name of the table that Hologres uses to receive data. In this example, the parameter is set to tb001.
      .option("decimal.precision", 38)
      .option("decimal.scale", 18)
      .option("batch.size", "<100>")// The number of data records to be imported in each batch. In this example, the parameter is set to 100.
      .save()
    ```

    If the Spark table contains data records of the DECIMAL type, run the following command to configure the precision of data:

    ```
    df.option("decimal.precision", 38)
      .option("decimal.scale", 18)
    ```


## Use the JDBC driver to import data to Hologres

Hologres is compatible with the PostgreSQL ecosystem and provides the JDBC or Open Database Connectivity \(ODBC\) drivers. You can use the JDBC driver to import Spark data to Hologres.

Before you use the JDBC driver to import data, download the [PostgresSQL JDBC version 42.2.xx or later](https://jdbc.postgresql.org/download.html) from the official website of PostgresSQL JDBC. Then, run the following command in the Spark shell to import data:

```
./bin/spark-shell --jars /path/to/postgresql-42.2.14.jar
```

You can import data in batch or stream mode.

-   Batch mode

    Prepare data in Spark and set the related parameters to import data to Hologres in batches. Execute the following statements:

    ```
    // Prepare data.
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
    
    // Set the related parameters to import data.
    .option("checkpointLocation", checkpointLocation)
    df.write.format("jdbc")
      .option("url", "jdbc:postgresql://endpoint:port/database")
      .option("driver", "org.postgresql.Driver")
      .option("dbtable", "<Table>")// The name of the table that Hologres uses to receive data.
      .option("user", "<AccessKey ID>")// The AccessKey ID of your Alibaba Cloud account.
      .option("password", "accesskey secret")// The AccessKey secret of your Alibaba Cloud account.
      .option("isolationLevel", "NONE")
      .save()
    ```

    The following table describes the required parameters.

    |Parameter|Description|
    |---------|-----------|
    |`jdbc:postgresql://endpoint:port/database`|    -   **endpoint**: the endpoint of the Hologres instance.
    -   **port**: the port number of the Hologres instance.
    -   **database**: the name of the Hologres database.
To obtain this information, log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance) and go to the details page of the Hologres instance. You can view the endpoint and the port number of the Hologres instance on the **Configurations** page and view the name of the database on the **Databases** page.|
    |table|The name of the Hologres table that is used to receive data.|
    |accesskey id|The AccessKey ID of your Alibaba Cloud account.You can obtain the AccessKey ID on the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page. |
    |accesskey secret|The AccessKey secret of your Alibaba Cloud account.You can obtain the AccessKey secret on the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page. |

-   Stream mode
    1.  Generate a JDBC JAR file.

        Run the following command to obtain the JDBC driver for Spark Community Edition or EMR Spark and compile code to generate a file named `emr-jdbc_2.11-2.1.0-SNAPSHOT.jar`:

        ```
        ./bin/spark-shell --jars /path_to/emr-jdbc_2.11-2.1.0-SNAPSHOT.jar,postgresql-42.2.14.jar --driver-class-path /path_to/emr-jdbc_2.11-2.1.0-SNAPSHOT.jar,postgresql-42.2.14.jar
        ```

    2.  Run the following command in the Spark shell to use the JDBC JAR file:

        ```
        ./bin/spark-shell --jars /path_to/emr-jdbc_2.11-2.1.0-SNAPSHOT.jar,postgresql-42.2.14.jar --driver-class-path /path_to/emr-jdbc_2.11-2.1.0-SNAPSHOT.jar,postgresql-42.2.14.jar
        ```

    3.  Prepare and import Spark data to Hologres.

        Create a table in Spark and write data to the table. Set the related parameters to import the data to Hologres in stream mode in real time.

        ```
        val wordCounts = lines.flatMap(_.split(" ")).groupBy("value").count()
        
        val query = wordCounts.writeStream
          .outputMode("complete")
          .format("jdbc2")
          .option("url", "<jdbc:postgresql://endpoint:port/database>")// The Hologres instance information.
          .option("driver", "org.postgresql.Driver")
          .option("dbtable", "<Table>")// The name of the table that Hologres uses to receive data.
          .option("user", "<AccessKey ID>")// The AccessKey ID of your Alibaba Cloud account.
          .option("password", "accesskey secret")// The AccessKey secret of your Alibaba Cloud account.
          .option("batchsize", "<100>")// The number of data records to be imported in each batch. In this example, the parameter is set to 100.
          .option("isolationLevel", "NONE")
          .option("checkpointLocation", checkpointLocation)
          .start()
        
        query.awaitTermination()
        ```

        **Note:**

        -   Set the `batchsize` parameter based on your needs. This improves the performance of data import.
        -   If the **Provider** parameter is set to jdbc2, the **EMR-SDK** delivers better performance of data import in stream mode. We recommend that you use jdbc2 for Spark Community Edition and EMR Spark.

## Data type mapping

The following table shows the mappings of data types between Spark and Hologres.

|Spark|Hologres|
|-----|--------|
|LongType|BIGINT|
|StringType|TEXT|
|DecimalType|NUMERIC\(38, 18\)|
|BooleanType|BOOL|
|DoubleType|DOUBLE PRECISION|
|TimestampType|TIMESTAMPTZ|

