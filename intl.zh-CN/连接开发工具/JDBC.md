---
keyword: [JDBC, Hologres]
---

# JDBC

Hologres为您提供完全兼容PostgreSQL的连接（JDBC/ODBC）接口，您可以通过该接口将SQL客户端工具连接至Hologres。本文为您介绍JDBC如何连接Hologres进行数据开发。

1.  下载配置。

    如果客户端工具默认集成了PostgreSQL数据库的驱动，直接使用工具自带的驱动即可。如果未集成驱动，请至[官网](https://mvnrepository.com/artifact/org.postgresql/postgresql)下载PostgreSQL JDBC Driver，建议您使用42.2.xx的版本。示例如下。

    ```
    <dependencies>
            <dependency>
                <groupId>org.postgresql</groupId>
                <artifactId>postgresql</artifactId>
                <version>42.2.8.jre6</version> 
                                                 
            </dependency>
        </dependencies>
    ```

2.  连接Hologres。

    示例语句如下。

    ```
    public class HologresTest {
    
        private void jdbcExample() throws Exception {
            String url = "jdbc:postgresql://{ENDPOINT}:{PORT}/{DBNAME}?user={ACCESS_ID}&password={ACCESS_KEY}";
            Connection conn = DriverManager.getConnection(url);
            Statement st = conn.createStatement();
            String sql = "SELECT * FROM table where xxx limit 100";
            ResultSet rs = st.executeQuery(sql);
            String c1 = rs.getString(1);
        }
        
        private void jdbcPreparedStmtExample() throws Exception {
            String sql = "insert into test values" +
                "(?, ?), " +
                "(?, ?), " +
                "(?, ?), " +
                "(?, ?), " +
                "(?, ?), " +
                "(?, ?), " +
                "(?, ?), " +
                "(?, ?), " +
                "(?, ?), " +
                "(?, ?)";
            try (PreparedStatement st = conn.prepareStatement(sql)) {
                for (int i = 0; i < 10; ++i) {
                    try {
                        for (int j = 0; j < 2 * 10; ++j) {
                            st.setString(j + 1, UUID.randomUUID().toString());
                        }
                        System.out.println("affected row => " + st.executeUpdate());
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

    更多关于Java的配置请参见文档[JAVA配置](https://javaconceptoftheday.com/difference-between-executequery-executeupdate-execute-in-jdbc/)。

3.  数据开发。

    JDBC成功连接Hologres后，您可以使用标准的开发语句进行数据开发。典型的应用场景示例如下。

    -   直接加速查询离线数据，请参见[通过创建外部表加速查询MaxCompute数据](/intl.zh-CN/数据接入/大数据/MaxCompute/通过创建外部表加速查询MaxCompute数据.md)。
    -   实时写入实时数据，请参见[实时计算实时写入数据至Hologres](/intl.zh-CN/数据接入/大数据/实时计算Flink版/实时计算实时写入数据至Hologres.md)。

