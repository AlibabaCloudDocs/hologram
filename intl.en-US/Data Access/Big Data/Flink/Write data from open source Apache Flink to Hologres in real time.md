---
keyword: [open source Apache Flink, write data in real time]
---

# Write data from open source Apache Flink to Hologres in real time

This topic describes how to write data from open source Apache Flink to Hologres in real time.

-   A Hologres instance is created and the instance is connected to a developer tool. Hologres is connected to the PostgreSQL client in this topic. For more information, see [Use Hologres from the PostgreSQL client](/intl.en-US/Quick Start/Use Hologres from the PostgreSQL client.md).
-   A Flink cluster is created. You can download a binary file from the Flink official website and create a Flink cluster that is deployed in Standalone mode. For more information about how to create a Flink cluster, see [Local Installation of Flink](https://ci.apache.org/projects/flink/flink-docs-release-1.11/try-flink/local_installation.html). A Flink 1.10 cluster is used in this topic.

## Procedure

1.  Create a Hologres result table.

    After the Hologres instance is connected to the developer tool, you must create a result table. The result table stores data that is written in real time. Sample statement:

    ```
    begin;
    create table order_details(user_id bigint, user_name text, item_id bigint, item_name text, price numeric(38, 2), province text, city text, ip text, longitude text, latitude text, sale_timestamp timestamptz not null, primary key(user_id, item_id));
    call set_table_property ('order_details', 'segment_key', 'sale_timestamp');
    commit;
    ```

2.  Download and compile the Java ARchive \(JAR\) file of Flink.

    1.  Download and install the JAR file named [hologres-flink-connector-1.10-jar-with-dependencies.jar](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/171614/cn_zh/1612665223522/hologres-flink-connector-1.10-jar-with-dependencies.jar) on which the Hologres connector depends. Sample statement:

        ```
        mvn install:install-file -Dfile=hologres-flink-connector-1.10-jar-with-dependencies.jar -DgroupId=org.apache.flink -DartifactId=hologress-flink-connector -Dversion=1.10 -Dpackaging=jar -DgeneratePom=true
        ```

    2.  Visit [official sample library of hologres](https://github.com/hologres/hologres-flink-examples). Then, download and compile the JAR file. Sample statement:

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
    flink run -c io.hologres.flink.example.HologresSinkExample ../hologres-flink-example/target/hologress-flink-examples-1.0.0-jar-with-dependencies.jar --endpoint $ENDPOINT --username $USERNAME --password $PASSWORD --database $DATABASE --tablename order_details
    ```

    The following table describes the parameters for the job.

    |Parameter|Description|Example|
    |---------|-----------|-------|
    |endpoint|The endpoint of your Hologres instance.Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance) and go to the instance details page. On the details page, you can obtain the endpoint from the **Configurations** section.

**Note:** If your Flink cluster is an on-premises Flink cluster, you must use the public endpoint to access the Hologres instance. If your Flink cluster is deployed in a virtual private cloud \(VPC\), you must use the VPC endpoint to access the Hologres instance.

|`ssseeee-cn-hangzhou.hologres.aliyuncs.com:80`|
    |username|The AccessKey ID of your Alibaba Cloud account.You can obtain the AccessKey ID on the [AccessKey Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page of the Alibaba Cloud Management Console.

|None|
    |password|The AccessKey secret of your Alibaba Cloud account.You can obtain the AccessKey secret on the [AccessKey Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page of the Alibaba Cloud Management Console.

|None|
    |database|The name of the Hologres database you want to access.|`hologres_demo`|
    |tablename|The name of the Hologres result table.|`order_details`|

4.  Query the data in Hologres.

    After you finish the preceding operations, you can query the data that is written into Hologres in real time. Sample statement:

    ```
    select count(1) from order_details;
    select item_id, sum(price) as total from order_details group by item_id limit 10;
    ```


