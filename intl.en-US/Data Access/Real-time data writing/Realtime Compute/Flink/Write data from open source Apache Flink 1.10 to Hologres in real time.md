---
keyword: [open source Apache Flink, write data in real time]
---

# Write data from open source Apache Flink 1.10 to Hologres in real time

This topic describes how to write data from open source Apache Flink 1.10 to Hologres in real time.

-   A Hologres instance is created and the instance is connected to a developer tool. Hologres is connected to the PostgreSQL client in this topic. For more information, see [PSQL quick start](/intl.en-US/Quick Start/PSQL quick start.md).
-   A Flink cluster is created. You can download a binary file from the Flink official website and create a Flink cluster that is deployed in Standalone mode. For more information about how to create a Flink cluster, see [Local Installation of Flink](https://ci.apache.org/projects/flink/flink-docs-release-1.11/try-flink/local_installation.html). A Flink 1.10 cluster is used in this topic.
-   A connection for importing data from Flink is created based on your business needs.

## Procedure

1.  Create a Hologres result table.

    After the Hologres instance is connected to the developer tool, you must create a result table. The result table stores data that is written in real time. Sample statements:

    ```
    begin;
    create table order_details(user_id bigint, user_name text, item_id bigint, item_name text, price numeric(38, 2), province text, city text, ip text, longitude text, latitude text, sale_timestamp timestamptz not null, primary key(user_id, item_id));
    call set_table_property ('order_details', 'segment_key', 'sale_timestamp');
    commit;
    ```

2.  Download and compile the Java ARchive \(JAR\) file of Flink.

    1.  Download and install the JAR file named [hologres-flink-connector-1.10-jar-with-dependencies.jar](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/207600/cn_zh/1617267912263/hologres-flink-connector-1.10-jar-with-dependencies.jar) on which the Hologres connector depends. Sample statement:

        ```
        mvn install:install-file -Dfile=hologres-flink-connector-1.10-jar-with-dependencies.jar -DgroupId=org.apache.flink -DartifactId=hologres-flink-connector -Dversion=1.10 -Dpackaging=jar -DgeneratePom=true
        ```

    2.  Visit [the official sample library of Hologres](https://github.com/hologres/hologres-flink-examples). Then, download and compile the JAR file. Sample statements:

        ```
        git clone https://github.com/hologres/hologres-flink-examples.git
        cd hologres-flink-examples
        git checkout -b example
        mvn package -DskipTests
        ```

3.  Submit a Flink job.

    After you compile the JAR file, you must configure and submit a Flink job. Sample statement:

    **Note:** In the sample statement, a command line is used to submit the Flink job. You can also submit the Flink job on the Flink web page.

    ```
    flink run -c io.hologres.flink.example.HologresSinkExample ../hologres-flink-example/target/hologres-flink-examples-1.0.0-jar-with-dependencies.jar --endpoint $ENDPOINT --username $USERNAME --password $PASSWORD --database $DATABASE --tablename order_details
    ```

    The following table describes the parameters for the job.

    |Parameter|Description|Example|
    |---------|-----------|-------|
    |endpoint|The endpoint of the Hologres instance to be connected to. You can view the endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

**Note:** If your Flink cluster is an on-premises Flink cluster, you must use the public endpoint to access the Hologres instance. If your Flink cluster is deployed in a virtual private cloud \(VPC\), you must use the VPC endpoint to access the Hologres instance.

|`ssseeee-cn-hangzhou.hologres.aliyuncs.com:80`|
    |username|The AccessKey ID of your Alibaba Cloud account. You can obtain the AccessKey ID from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.

|N/A|
    |password|The AccessKey secret of your Alibaba Cloud account. You can obtain the AccessKey secret from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.

|N/A|
    |database|The name of the Hologres database to be connected to.|`hologres_demo`|
    |tablename|The name of the Hologres table that is used to receive data.|`order_details`|

4.  Query the data in Hologres.

    After you finish the preceding operations, you can query the data that is written to Hologres in real time. Sample statements:

    ```
    select count(1) from order_details;
    select item_id, sum(price) as total from order_details group by item_id limit 10;
    ```


