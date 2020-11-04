---
keyword: [自增序列, Serial, Bigserial]
---

# 自增序列Serial

交互式分析Hologres兼容Postgres生态，支持在建表时将字段的类型定义为Serial和Bigserial来定义自增的字段。

## 使用限制

-   Hologres不能指定Serial的递增步长及初始值参数，初始值默认设置为1。
-   Hologres暂不支持Smallserial。

## 语法

Hologres在建表时可以将字段的类型定义为Serial和Bigserial，Serial表示INT4类型的自增字段，Bigserial表示INT8类型的自增字段。

示例配置Serial类型的自增序列语句如下。

```
CREATE TABLE tablename (
    colname SERIAL
);
```

## 示例

JDBC连接Hologres，实现自增序列，示例语句如下。

```
package test;

import java.sql.*;

public class HoloSerial {

    private static void Init(Connection conn) throws Exception {
        try (Statement stmt = conn.createStatement()) {
            stmt.execute("drop table if exists test_tb;");
            stmt.execute("create table if not exists test_tb(pk serial primary key, f1 text);");
        }
    }

    private static void TestSerial(Connection conn) throws Exception {
        try (PreparedStatement stmt = conn.prepareStatement("insert into test_tb(f1) values(?)")) {
            for (int i = 0; i < 100; ++i) {
                stmt.setString(1, String.valueOf(i + 1));
                int affected_rows = stmt.executeUpdate();
                System.out.println("affected rows => " + affected_rows);
            }
        }

        try (PreparedStatement stmt = conn.prepareStatement("select * from test_tb order by pk asc")) {
            try(ResultSet rs = stmt.executeQuery()) {
                while(rs.next()) {
                    String res = rs.getObject(1).toString() + "\t" + rs.getObject(2).toString();
                    System.out.println(res);
                }
            }
        }
    }

    public static void main(String[] args) throws Exception {
        Class.forName("org.postgresql.Driver").newInstance();
        String host = "127.0.0.1:13737";
        String db = "postgres";
        String user = "xx";
        String password = "xx";
        String url = "jdbc:postgresql://" + host + "/" + db;
        try (Connection conn = DriverManager.getConnection(url, user, password)) {
            Init(conn);

            TestSerial(conn);
        }
    }
}
```

使用SQL语句实现自增序列，示例语句如下。

```
create table if not exists test_tb(pk serial primary key, f1 text);

insert into test_tb(f1) values('1');
insert into test_tb(f1) values('2');
insert into test_tb(f1) values('3');

select * from test_tb order by pk asc;
```

