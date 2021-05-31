---
keyword: [query key-value pairs, Hologres]
---

# Query key-value pairs

Hologres provides interactive analysis and real-time data warehousing services. It supports tens of billions of datasets. Compatible with the SQL syntax, Hologres provides low latency of less than 10 milliseconds and million-level queries per second \(QPS\) for point queries of key-value pairs. This topic provides some best practices of Hologres in point query scenarios, such as creating a table and querying data.

## Create a table

When you create a Hologres table that supports point queries, take note of the following items:

-   Each key field that you want to query must be included in the primary key.
-   The table must be a row-oriented table.
-   We recommend that you connect to a Hologres instance by using its virtual private cloud \(VPC\) endpoint.

For example, you can execute the following statements to create a table named test\_kv\_table:

```
--Create a row-oriented table named test_kv_table and set the key field as the primary key.
begin;
create table test_kv_table(
    key text primary key,
    value text
);
call set_table_property('test_kv_table', 'orientation', 'row');
commit;
```

## Query data

After you import data to the newly created table, you can perform point queries on the data. You can execute the following sample statements based on different scenarios:

-   Query a single key-value pair at a time.

    ```
    select * from test_kv_table where key  = '1';
    ```

-   Query multiple key-value pairs at a time.

    ```
    select * from test_kv_table where key  in ('1', '2', '3');
    ```

-   Query data in Java.

    In Java, you can use a PreparedStatement object to query key-value pairs, as demonstrated in the following example.

    **Note:**

    -   We recommend that you connect to a Hologres instance by using its VPC endpoint.
    -   A PreparedStatement object provides better performance for querying key-value pairs.
    -   A PreparedStatement object is reusable. You do not need to create a PreparedStatement object for each query.
    ```
    // Query multiple key-value pairs where the values are integers from 1 to 100.   
     private static void testKV(Connection conn) throws Exception {
            String sql = "select * from test_kv_table where key = ?" ;
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


## Sample Java code for querying key-value pairs

The following sample code shows you how to query key-value pairs in Java. In the sample code, a row-oriented table named test\_kv\_table is created. The key field is set as the primary key. A PreparedStatement object is used to query multiple key-value pairs at a time and the query result is returned.

```
package test;

import org.postgresql.jdbc.PgConnection;
import java.sql. *;

// Create a row-oriented table named test_kv_table and set the key field as the primary key.
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
// Query multiple key-value pairs where the values are integers from 1 to 100.
    private static void testKV(Connection conn) throws Exception {
        String sql = "select *from test_kv_table where key = ?" ;
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
// Return the query result.
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

