---
keyword: [COPY, import client data, Hologres]
---

# Use the COPY statement to import on-premises data to Hologres

This topic describes how to use the COPY statement to import on-premises data to Hologres.

## Limits

When you use the COPY statement to import data to Hologres, pay attention to the following points:

-   The COPY statement supports the data types supported by Hologres. For more information, see [Data types](/intl.en-US/Hologres SQL/Data types/Data types.md).
-   To use the COPY statement to import data from a partitioned table to Hologres, you can import the data only to child partitioned tables rather than parent partitioned tables.
-   You must execute the `COPY FROM STDIN` statement to import data to Hologres. The `COPY TO` statement is not supported.

For more information about the `COPY` statement, see [COPY](https://www.postgresql.org/docs/9.2/sql-copy.html) in PostgreSQL documentation.

## Syntax

You can use the `COPY FROM` statement to import data from stdin of the client to Hologres.

Hologres allows you to execute the COPY statement in the following format:

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

## Parameters

The following table describes the parameters in the syntax.

|Parameter|Description|
|---------|-----------|
|table\_name|The name of the Hologres table that is used to receive data.|
|STDIN|Specifies to import data from stdin of the client.|
|FORMAT|The type of the data to be imported. Valid values: text and csv. Default value: text. |
|DELIMITER|The delimiter that is used to separate columns. The default delimiter is a tab character in the text format or a comma \(,\) in the CSV format. Example: `DELIMITER AS ','`. |

## Examples

-   Import data from stdin to Hologres by executing the following statements:

    ```
    -- Create a table in Hologres. 
    CREATE TABLE copy_test (
      id    int,
      age   int,
      name  text
    ) ;
    
    -- Import data to the created Hologres table. 
    COPY copy_test FROM STDIN WITH DELIMITER AS ',' NULL AS '';
    53444,24,wangming
    55444,38,ligang
    55444,38,luyong
    \.
    
    -- Query data in the Hologres table. 
    SELECT * FROM copy_test;
    ```

    **Note:** A PostgreSQL client allows you to import data from stdin. HoloStudio and HoloWeb do not allow you to use command lines to import data from stdin to Hologres.

-   Import a CSV file from stdin to Hologres by executing the following statements:

    ```
    -- Create a table in Hologres. 
    CREATE TABLE partsupp ( ps_partkey          integer not null,
                            ps_suppkey     integer not null,
                            ps_availqty    integer not null,     
                            ps_supplycost  float  not null,
                            ps_comment     text not null );
    
    -- Import a CSV file to the created Hologres table. 
    COPY partsupp FROM STDIN WITH DELIMITER '|' CSV;  
    1|2|3325|771.64|final theodolites 
    1|25002|8076|993.49|ven ideas
    \.
    
    -- Query data in the Hologres table. 
    SELECT * FROM partsupp;
    ```

    **Note:** A PostgreSQL client allows you to import data from stdin. HoloStudio and HoloWeb do not allow you to use command lines to import a CSV file from stdin to Hologres.

-   Import an on-premises file to Hologres by executing the following statement:

    ```
    psql -U <username> -p <port> -h <endpoint> -d <databasename> -c "COPY <table> from stdin with delimiter '|' csv;" <<filename>;
    ```

    **Note:** A PostgreSQL client allows you to import data from stdin. HoloStudio and HoloWeb do not allow you to use command lines to import an on-premises file from stdin to Hologres. When you use a PostgreSQL client, you can import data only from stdin. Therefore, you must convert the format of the data in the imported file to the standard input format.

    The following example shows you how to execute a statement to import an on-premises file from stdin to Hologres:

    -   Execute the statement to import the on-premises file named copy\_test from stdin to Hologres.

        ![Execute the statement](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7771290261/p264751.png)

        The imported standard file includes the following content:

        ```
        01,01,name1
        02,01,name2
        03,01,name3
        04,01,name4
        ```

    -   After the statement is executed, go back to the PostgreSQL client. Then, you can query newly imported data, as shown in the following figure.

        ![Query results](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7771290261/p264730.png)

-   Use CopyManager to import a Java Database Connectivity \(JDBC\) client file to Hologres by executing the following statements:

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
            props.setProperty("user", "AAA");// The AccessKey ID of your Alibaba Cloud account. 
        //set db password
            props.setProperty("password", "BBB");// The AccessKey secret of your Alibaba Cloud account. 
            return DriverManager.getConnection(url, props);
        }
    
        /**
         * Import the file to the Hologres database. 
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


## Upload an on-premises file to Hologres with a few clicks in a visualized manner

You can upload on-premises files to Hologres with a few clicks in a visualized manner by using HoloWeb. For more information, see [Import an on-premises file to Hologres](/intl.en-US/Common Development Tools/HoloWeb/System Mangement/Data Synchronization/Import an on-premises file to Hologres.md).

