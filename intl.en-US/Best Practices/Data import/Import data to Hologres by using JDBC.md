---
keyword: [best practices, JDBC, import data to Hologres]
---

# Import data to Hologres by using JDBC

This topic describes how to import data to Hologres by using Java Database Connectivity \(JDBC\).

## Limits

To import data to Hologres, you can use only PostgreSQL JDBC Driver V42.2.18 or later.

## Usage notes

-   We recommend that you use a virtual private cloud \(VPC\) network to test data import performance after you connect to Hologres. The Internet cannot be used to test data import performance.
-   Hologres does not support multiple inserts in a transaction. Therefore, if you need to write data to Hologres multiple times in a transaction, you must enable the auto-commit mode. If you use PostgreSQL JDBC Driver, the auto-commit mode is enabled by default.

    If the `ERROR: INSERT in transaction is not supported now` error is reported, you must enable the auto-commit mode.

    ```
    Connection conn = DriverManager.getConnection(url, user, password);
    conn.setAutoCommit(true);
    ```


## Connect to Hologres by using JDBC

1.  Install a JDBC driver.

    Hologres is compatible with the PostgreSQL 11 ecosystem. You can connect to Hologres by using [PostgreSQL JDBC Driver](https://mvnrepository.com/artifact/org.postgresql/postgresql?spm=a2c4g.11186623.2.10.4d6430edjM1H06).

    We recommend that you use PostgreSQL JDBC Driver V40.2.18 or later. In the following example, PostgreSQL JDBC Driver V42.2.18 is used:

    ```
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <version>42.2.18</version>
    </dependency>
    ```

2.  Use JDBC to connect to Hologres.

    Execute the following statement to connect to Hologres by using JDBC:

    ```
    jdbc:postgresql://<host>:<port>/<dbname>?reWriteBatchedInserts=true
    ```

    **Note:** If you add the `reWriteBatchedInserts=true` connection parameter to a JDBC URL, multiple inserts are enabled. This improves data import performance.

    The following table describes the parameters in the syntax.

    |Parameter|Description|
    |---------|-----------|
    |host|The endpoint of the Hologres instance. You can view the endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
    |port|The port number of the Hologres instance. You can view the port number of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
    |dbname|The name of the database to which you want to connect.|

3.  Import data to Hologres.

    You can import data by using JDBC statements or prepared statements.

    In most cases, we recommend that you use prepared statements and set the number of data entries imported at a time to 256 or multiples of 256. This is because the client caches the SQL compilation results when prepared statements are executed. This reduces the latency of data import and improves the throughput.

    The following examples demonstrate how to import data by executing prepared statements:

    -   Example 1: Execute the following statements to import data in batches:

        ```
        /* Import data in batches by executing prepared statements. * /
        /* In this example, the number of data entries imported at a time is set to 256. * /
        private static void WriteBatchWithPreparedStatement(Connection conn) throws Exception {
            try (PreparedStatement stmt = conn.prepareStatement("insert into test_tb values (?,?,?,?)")) {
                int batchSize = 256;
                for (int i = 0; i < batchSize; ++i) {
                    stmt.setInt( 1, 1000 + i);
                    stmt.setString( 2, "1");
                    SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
                    Date parsedDate = dateFormat.parse("1990-11-11 00:00:00");
                    stmt.setTimestamp( 3, new java.sql.Timestamp(parsedDate.getTime()));
                    stmt.setDouble( 4 , 0.1 );
                    stmt.addBatch();
                }
                stmt.executeBatch();
            }
        }
        ```

    -   Example 2: Add the `INSERT ON CONFLICT` statement to insert and update the data when you execute prepared statements. Execute the following statements:

        **Note:** When you use the `INSERT ON CONFLICT` statement, a primary key must be defined for the table.

        ```
        private static void InsertOverwrite(Connection conn) throws Exception {
            try (PreparedStatement stmt = conn.prepareStatement("insert into test_tb values (?,?,?,?), (?,?,?,?), (?,?,?,?), (?,?,?,?), (?,?,?,?), (?,?,?,?) on conflict(pk) do update set f1 = excluded.f1, f2 = excluded.f2, f3 = excluded.f3")) {
                int batchSize = 6;
                for (int i = 0; i < batchSize; ++i) {
                    stmt.setInt(i * 4 + 1, i);
                    stmt.setString(i * 4 + 2, "1");
                    SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
                    Date parsedDate = dateFormat.parse("1990-11-11 00:00:00");
                    stmt.setTimestamp(i * 4 + 3, new java.sql.Timestamp(parsedDate.getTime()));
                    stmt.setDouble(i * 4 + 4, 0.1);
                }
                int affected_rows = stmt.executeUpdate();
                System.out.println("affected rows => " + affected_rows);
            }
        }
        ```


## Example

Execute the following statements to import data to Hologres by using JDBC:

```
package test;

import java.sql.*;
import java.text.SimpleDateFormat;
import java.util.Date;

public class WriteHolo {

    private static void Init(Connection conn) throws Exception {
        try (Statement stmt = conn.createStatement()) {
            stmt.execute("drop table if exists test_tb;");
            stmt.execute("create table if not exists test_tb(pk bigint primary key, f1 text, f2 timestamptz, f3 float);");
        }
    }

   

    private static void WriteBatchWithPreparedStatement(Connection conn) throws Exception {
        try (PreparedStatement stmt = conn.prepareStatement("insert into test_tb values (?,?,?,?)")) {
            int batchSize = 256;
            for (int i = 0; i < batchSize; ++i) {
                stmt.setInt( 1, 1000 + i);
                stmt.setString( 2, "1");
                SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
                Date parsedDate = dateFormat.parse("1990-11-11 00:00:00");
                stmt.setTimestamp( 3, new java.sql.Timestamp(parsedDate.getTime()));
                stmt.setDouble( 4, 0.1);
                stmt.addBatch();
            }
            stmt.executeBatch();
        }
    }

    private static void InsertOverwrite(Connection conn) throws Exception {
        try (PreparedStatement stmt = conn.prepareStatement("insert into test_tb values (?,?,?,?), (?,?,?,?), (?,?,?,?), (?,?,?,?), (?,?,?,?), (?,?,?,?) " +
                "on conflict(pk) do update set f1 = excluded.f1, f2 = excluded.f2, f3 = excluded.f3")) {
            int batchSize = 6;
            for (int i = 0; i < batchSize; ++i) {
                stmt.setInt(i * 4 + 1, i);
                stmt.setString(i * 4 + 2, "1");
                SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
                Date parsedDate = dateFormat.parse("1990-11-11 00:00:00");
                stmt.setTimestamp(i * 4 + 3, new java.sql.Timestamp(parsedDate.getTime()));
                stmt.setDouble(i * 4 + 4, 0.1);
            }
            int affected_rows = stmt.executeUpdate();
            System.out.println("affected rows => " + affected_rows);
        }
    }

    public static void main(String[] args) throws Exception {
        Class.forName("org.postgresql.Driver").newInstance();
        String host = "127.0.0.1:13737";
        String db = "postgres";
        String user = "xx";
        String password = "xx";
        String url = "jdbc:postgresql://" + host + "/" + db+"?reWriteBatchedInserts=true";
        try (Connection conn = DriverManager.getConnection(url, user, password)) {
            Init(conn);

            WriteBatchWithPreparedStatement(conn);

            InsertOverwrite(conn);
        }
    }
}
```

