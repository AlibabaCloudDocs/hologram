---
keyword: [最佳实践, JDBC, 写入Hologres]
---

# 使用JDBC写入数据至Hologres的最佳实践

本文为您介绍如何通过JDBC将数据高效的写入Hologres。

## 使用限制

通过JDBC写入数据至Hologres需要使用42.2.2及以上的Postgres JDBC Driver。

## 注意事项

-   使用JDBC连接Hologres后，如果您需要测试写入数据的性能，建议使用VPC网络。公共网络由于自身原因，无法达到性能测试的目标。
-   Hologres当前不支持在一个事务中多次写入，因此需要关闭autoCommit（JDBC的autoCommit默认关闭），并且不要显示的在代码中执行提交动作。显示关闭autoCommit的语句如下。

    ```
    Connection conn = DriverManager.getConnection(url, user, password);
    conn.setAutoCommit(true);
    ```

    如果出现`ERROR: INSERT in transaction is not supported now`报错，则需要关闭autoCommit。


## 通过JDBC连接Hologres

1.  安装JDBC Driver。

    Hologres兼容PostgreSQL 11生态，您可以通过Postgres的[JDBC Driver](https://mvnrepository.com/artifact/org.postgresql/postgresql?spm=a2c4g.11186623.2.10.4d6430edjM1H06)连接Hologres的服务。

    建议您选择40.2.x以上的JDBC Driver版本。示例在pom.xml文件中添加42.2.5版本的JDBC Driver。

    ```
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <version>42.2.5</version>
    </dependency>
    ```

2.  连接Hologres。

    在JDBC中连接Hologres的语句如下。

    ```
    jdbc:postgresql://<host>:<port>/<dbname>?reWriteBatchedInserts=true
    ```

    **说明：** 在JDBC URL中添加`reWriteBatchedInserts=true`配置，系统会以批量写入的方式提交作业，性能更好。

    参数描述如下表所示。

    |参数|描述|
    |--|--|
    |host|Hologres实例的网络地址。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取网络地址。 |
    |port|Hologres实例的网络端口。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取网络端口。 |
    |dbname|Hologres实例的数据库名称。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**DB管理**获取数据库名称。 |

3.  写入数据至Hologres。

    您可以通过JDBC的Statement或Prepared Statement模式写入数据。

    通常情况下，推荐您使用Prepare Statment模式，并且设置批量写入数据的条数为256的倍数（建议最低设置为256条）。因为使用Prepare Statment模式时，服务端会缓存SQL编译的结果，从而降低写入数据的延时，并提高吞吐量。

    使用Prepared Statement模式写入数据的示例如下：

    -   示例一：使用Prepared Statement模式批量写入数据，语句如下。

        ```
        /*通过Prepared Statement模式批量写入数据*/
        /*该示例中，设置的批量写入大小batchSize为256*/
        private static void WriteBatchWithPreparedStatement(Connection conn) throws Exception {
            try (PreparedStatement stmt = conn.prepareStatement("insert into test_tb values (?,?,?,?)")) {
                int batchSize = 256;
                for (int i = 0; i < batchSize;   i) {
                    stmt.setInt( 1, 1000   i);
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

    -   示例二：使用Prepared Statement模式写入数据的同时，添加Postgres的`INSERT ON CONFLICT`功能，实现写入时更新覆盖原有数据。语句如下。

        **说明：** 使用`INSERT ON CONFLICT`语句时目标表必须定义主键。

        ```
        private static void InsertOverwrite(Connection conn) throws Exception {
            try (PreparedStatement stmt = conn.prepareStatement("insert into test_tb values (?,?,?,?), (?,?,?,?), (?,?,?,?), (?,?,?,?), (?,?,?,?), (?,?,?,?) on conflict(pk) do update set f1 = excluded.f1, f2 = excluded.f2, f3 = excluded.f3")) {
                int batchSize = 6;
                for (int i = 0; i < batchSize;   i) {
                    stmt.setInt(i * 4   1, i);
                    stmt.setString(i * 4   2, "1");
                    SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
                    Date parsedDate = dateFormat.parse("1990-11-11 00:00:00");
                    stmt.setTimestamp(i * 4   3, new java.sql.Timestamp(parsedDate.getTime()));
                    stmt.setDouble(i * 4   4, 0.1);
                }
                int affected_rows = stmt.executeUpdate();
                System.out.println("affected rows => "   affected_rows);
            }
        }
        ```


## 示例

使用JDBC写入数据至Hologres的完整示例，语句如下。

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
            for (int i = 0; i < batchSize;   i) {
                stmt.setInt( 1, 1000   i);
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
        try (PreparedStatement stmt = conn.prepareStatement("insert into test_tb values (?,?,?,?), (?,?,?,?), (?,?,?,?), (?,?,?,?), (?,?,?,?), (?,?,?,?) "  
                "on conflict(pk) do update set f1 = excluded.f1, f2 = excluded.f2, f3 = excluded.f3")) {
            int batchSize = 6;
            for (int i = 0; i < batchSize;   i) {
                stmt.setInt(i * 4   1, i);
                stmt.setString(i * 4   2, "1");
                SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
                Date parsedDate = dateFormat.parse("1990-11-11 00:00:00");
                stmt.setTimestamp(i * 4   3, new java.sql.Timestamp(parsedDate.getTime()));
                stmt.setDouble(i * 4   4, 0.1);
            }
            int affected_rows = stmt.executeUpdate();
            System.out.println("affected rows => "   affected_rows);
        }
    }

    public static void main(String[] args) throws Exception {
        Class.forName("org.postgresql.Driver").newInstance();
        String host = "127.0.0.1:13737";
        String db = "postgres";
        String user = "xx";
        String password = "xx";
        String url = "jdbc:postgresql://"   host   "/"   db "?reWriteBatchedInserts=true";
        try (Connection conn = DriverManager.getConnection(url, user, password)) {
            Init(conn);

            WriteBatchWithPreparedStatement(conn);

            InsertOverwrite(conn);
        }
    }
}
```

