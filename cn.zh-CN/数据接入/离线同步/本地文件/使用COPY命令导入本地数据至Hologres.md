---
keyword: [COPY, 导入客户端数据, Hologres]
---

# 使用COPY命令导入本地数据至Hologres

本文为您介绍如何使用COPY命令导入本地的数据至Hologres。

## 使用限制

使用COPY命令导入数据至Hologres的限制说明如下：

-   当前COPY命令支持的数据类型与Hologres引擎支持的数据类型一致，详情参见[数据类型汇总](/cn.zh-CN/Hologres SQL/数据类型/数据类型汇总.md)。
-   如果导入的是分区表数据，则Hologres只支持导入数据至分区表子表，不支持导入数据至分区表父表。
-   Hologres仅支持使用`COPY FROM STDIN`命令导入数据，不支持使用`COPY TO`命令导入。

更多关于`COPY`命令的用法请参见[PostgreSQL官网文档COPY](https://www.postgresql.org/docs/9.2/sql-copy.html)。

## 命令格式

`COPY FROM`命令用于从客户端的标准输入导入数据至Hologres。

Hologres支持的COPY语句格式如下。

```
COPY table_name [ ( column_name [, ...] ) ]
    FROM STDIN
    [ [ WITH ] ( option [, ...] ) ]

   where option can be one of:

    FORMAT format_name
    DELIMITER 'delimiter_character'
    NULL 'null_string'
    HEADER [ boolean ]
    QUOTE 'quote_character'
    ESCAPE 'escape_character'
    FORCE_QUOTE { ( column_name [, ...] ) | * }
    FORCE_NOT_NULL ( column_name [, ...] )
```

## 参数说明

参数说明如下表所示。

|参数|描述|
|--|--|
|table\_name|Hologres接收数据的表名称。|
|STDIN|指定从客户端使用标准输入。|
|FORMAT|支持TEXT和CSV格式。默认为TEXT格式。 |
|DELIMITER|指定的字段分隔符。文本格式默认为制表符，CSV格式默认为逗号。例如`DELIMITER AS ','`。 |

## 使用示例

-   使用STDIN导入数据至Hologres，命令如下。

    ```
    --创建Hologres表。
    CREATE TABLE copy_test (
      id    int,
      age   int,
      name  text
    ) ;
    
    --导入数据至Hologres表。
    COPY copy_test FROM STDIN WITH DELIMITER AS ',' NULL AS '';
    53444,24,wangming
    55444,38,ligang
    55444,38,luyong
    \.
    
    --查询表中的数据。
    SELECT * FROM copy_test;
    ```

    **说明：** psql客户端支持使用STDIN导入数据，HoloStudio及HoloWeb暂不支持使用命令行方式导入数据。

-   使用STDIN方式导入CSV格式的文件至Hologres。

    ```
    --创建Hologres表。
    CREATE TABLE partsupp ( ps_partkey          integer not null,
                            ps_suppkey     integer not null,
                            ps_availqty    integer not null,     
                            ps_supplycost  float  not null,
                            ps_comment     text not null );
    
    --导入CSV格式的文件至Hologres表。
    COPY partsupp FROM STDIN WITH DELIMITER '|' CSV;  
    1|2|3325|771.64|final theodolites 
    1|25002|8076|993.49|ven ideas
    \.
    
    --查询表中的数据。
    SELECT * FROM partsupp;
    ```

    **说明：** psql客户端支持使用STDIN导入数据，HoloStudio及HoloWeb暂不支持使用命令行方式通过STDIN导入CSV格式的文件。

-   导入本地文件至Hologres，命令如下。

    ```
    psql -U <username> -p <port> -h <endpoint> -d <databasename> -c "COPY <table> from stdin with delimiter '|' csv;" <<filename>;
    ```

    **说明：** psql客户端支持使用STDIN导入数据，HoloStudio及HoloWeb暂不支持使用命令行方式通过STDIN导入本地文件。由于psql客户端仅支持STDIN（标准输入）方式导入数据，需要将文件数据转换为标准输入格式。

    如下示例将指导您在终端执行命令导入本地文件至Hologres。

    -   输入命令导入本地文件copy\_test至Hologres。

        ![执行命令](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0202658161/p264751.png)

        其中，插入的标准文件内容如下：

        ```
        01,01,name1
        02,01,name2
        03,01,name3
        04,01,name4
        ```

    -   执行完成后，回到psql客户端可以查询新插入的数据，如下图所示。

        ![查询结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0202658161/p264730.png)

-   使用CopyManager导入JDBC客户端的文件至Hologres，命令如下。

    ```
    package com.aliyun.hologram.test.jdbc;
    
    import java.io.FileInputStream;
    import java.io.FileOutputStream;
    import java.io.IOException;
    import java.sql.*;
    import java.util.Properties;
    
    import org.postgresql.copy.CopyManager;
    import org.postgresql.core.BaseConnection;
    
    public class jdbcCopyFile {
    
        public static void main(String args[]) throws Exception {
            System.out.println(copyFromFile(getConnection(), "/Users/feng/Workspace/region.tbl", "region"));
        }
    
        public static Connection getConnection() throws Exception {
            Class.forName("org.postgresql.Driver");
            String url = "jdbc:postgresql://endpoint:port/dbname";
            Properties props = new Properties();
        //set db user
            props.setProperty("user", "AAA");//当前账号的AccessKey ID。
        //set db password
            props.setProperty("password", "BBB");//当前账号的AccessKey SECRET。
            return DriverManager.getConnection(url, props);
        }
    
        /**
         * 导入文件至数据库。
         * 
         * @param connection
         * @param filePath
         * @param tableName
         * @return
         * @throws SQLException
         * @throws IOException
         */
        public static long copyFromFile(Connection connection, String filePath, String tableName)
                throws SQLException, IOException {
            long count = 0;
            FileInputStream fileInputStream = null;
    
            try {
                CopyManager copyManager = new CopyManager((BaseConnection) connection);
                fileInputStream = new FileInputStream(filePath);
                count = copyManager.copyIn("COPY " + tableName + " FROM STDIN delimiter '|' csv", fileInputStream);
            } finally {
                if (fileInputStream != null) {
                    try {
                        fileInputStream.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
            return count;
        }
    }
    ```


## 可视化一键导入本地文件

您可以通过HoloWeb可视化一键上传本地文件，具体操作请参见[一键上传本地文件](/cn.zh-CN/连接开发工具/HoloWeb/系统管理/数据同步/一键上传本地文件.md)

