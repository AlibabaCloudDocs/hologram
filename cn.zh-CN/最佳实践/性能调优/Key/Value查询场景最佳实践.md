---
keyword: [Key/Value查询, Hologres]
---

# Key/Value查询场景最佳实践

Hologres是一款服务分析一体化的实时数仓，支持海量数据集（百亿以上），基于SQL提供低延时（10ms以下）、高QPS（上百万每秒）的Key/Value点查服务。本文主要指导您在点查场景的最佳实践，包括建表和查询方式等。

## 创建表

创建点查场景的表时，您需要注意如下几点：

-   Key/Value点查的Key字段需要设置为主键（Primary Key）。
-   表的存储类型需要设置为行存。
-   建议连接Hologres实例时使用VPC网络的域名。

具体创建表的示例语句如下：

```
--创建一张行存表test_kv_table并将key字段设置为主键
begin;
create table test_kv_table(
    key text primary key,
    value text
);
call set_table_property('test_kv_table', 'orientation', 'row');
commit;
```

## 数据查询

当您将数据导入表之后就可以对表数据进行点查询。具体查询方式如下所示：

-   一次查询单个Key

    ```
    select * from test_kv_table where key  = '1';
    ```

-   一次查询多个Key

    ```
    select * from test_kv_table where key  in ('1', '2', '3');
    ```

-   使用Java查询

    以Java为例，您可以使用Prepared Statement来进行Key/Value查询，示例如下。

    **说明：**

    -   建议使用Hologres的VPC网络的域名连接。
    -   使用Prepared Statement进行Key/Value查询会得到更好的查询性能。
    -   Prepared Statement可以复用，不需要每次查询都新建一个Prepared Statement对象。
    ```
    //查询取值1-100的多个key   
     private static void testKV(Connection conn) throws Exception {
            String sql = "select * from test_kv_table where key = ?";
            try (PreparedStatement stmt = conn.prepareStatement(sql)) {
                for (int i = 0; i < 100; ++i) {
                    stmt.setString(1, Integer.toString(i));
                    long begin = System.currentTimeMillis();
                    try (ResultSet rs = stmt.executeQuery()) {
                    long cost = System.currentTimeMillis() - begin;
                        while(rs.next()) {
                            System.out.println("data => " + rs.getObject(1).toString() + " " + rs.getObject(2).toString() + " latency => [" + cost + "]ms");
                        }
                    }
                }
            }
        }
    ```


## Java使用示例

如下内容为完整的Java使用示例，指导您创建一张行存表test\_kv\_table并将key字段设置为主键，一次查询多个Key并输出查询结果。

```
package test;

import org.postgresql.jdbc.PgConnection;
import java.sql.*;

//创建一张行存表test_kv_table并将key字段设置为主键
public class TestPointQuery {

    private static void init(Connection conn) throws Exception {
        try (Statement stmt = conn.createStatement()) {
            stmt.execute("drop table if exists test_kv_table;");
            stmt.execute("begin;");
            stmt.execute("create table if not exists test_kv_table(key text primary key, value text);");
            stmt.execute("call set_table_property('test_kv_table', 'orientation', 'row');");
            stmt.execute("call set_table_property('test_kv_table', 'shard_count', '20');");
            stmt.execute("end;");
            stmt.execute("insert into test_kv_table select i, i from generate_series(1, 10000)i");
        }
    }
//查询取值1-100的多个key
    private static void testKV(Connection conn) throws Exception {
        String sql = "select *from test_kv_table where key = ?";
        try (PreparedStatement stmt = conn.prepareStatement(sql)) {
            for (int i = 0; i < 100; ++i) {
                stmt.setString(1, Integer.toString(i));
                long begin = System.currentTimeMillis();
                try (ResultSet rs = stmt.executeQuery()) {
                long cost = System.currentTimeMillis() - begin;
                    while(rs.next()) {
                        System.out.println("data => " + rs.getObject(1).toString() + " " + rs.getObject(2).toString() + " latency => [" + cost + "]ms");
                    }
                }
            }
        }
    }
//输出查询结果
    public static void main(String[] args) throws Exception {
        Class.forName("org.postgresql.Driver").newInstance();
        String host = "";
        String db = "";
        String user = "";
        String password = "";
        String url = "jdbc:postgresql://" + host + "/" + db;

        try (PgConnection conn = (PgConnection) DriverManager.getConnection(url, user, password)) {
            System.out.println("init the test_kv_table for testing");
            init(conn);
            System.out.println("run test on test_kv_table");
            testKV(conn);
        }
    }
}
```

