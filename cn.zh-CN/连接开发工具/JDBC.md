---
keyword: [JDBC, Hologres]
---

# JDBC

Hologres为您提供完全兼容PostgreSQL的连接（JDBC/ODBC）接口，您可以通过该接口将SQL客户端工具连接至Hologres。本文为您介绍JDBC如何连接Hologres进行数据开发。

1.  下载配置。

    -   如果客户端工具默认集成了PostgreSQL数据库的驱动，直接使用工具自带的驱动即可。如果未集成驱动，请至[官网](https://mvnrepository.com/artifact/org.postgresql/postgresql)下载PostgreSQL JDBC Driver，建议您使用42.2.18以上版本的JDBC。示例如下。

        ```
        <dependencies>
                <dependency>
                    <groupId>org.postgresql</groupId>
                    <artifactId>postgresql</artifactId>
                    <version>42.2.18.jre6</version> 
                                                     
                </dependency>
            </dependencies>
        ```

    -   您也可以使用Hologres特制的驱动。示例如下。

        ```
        <dependency>
          <groupId>com.alibaba.hologres</groupId>
          <artifactId>postgresql-holo</artifactId>
          <version>42.2.18.4</version>
        </dependency>
        ```

        包含如下新功能：

        -   支持通过主表写入分区表。
        -   支持yyyyMMddHHmmss型字符串写入timestamp时类型自动转换。
        -   新增若干参数，具体如下。

            |参数名|说明|默认值|
            |---|--|---|
            |dynamicPartition|当取值为true时，写入的分区子表如果不存在则自动创建。|false|
            |inputNumberAsEpochMsForDatetimeColumn|当取值为true时，Long型对象写入timestamp列，会将Long型对象作为epoch毫秒对待。|false|
            |inputStringAsEpochMsForDatetimeColumn|当取值为true时，String型对象写入 timestamp列，会将String型对象作为epoch毫秒对待。|false|

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

    -   直接加速查询离线数据，请参见[通过创建外部表加速查询MaxCompute数据](/cn.zh-CN/数据接入/离线同步/MaxCompute/通过创建外部表加速查询MaxCompute数据.md)。
    -   实时写入实时数据，请参见[Hologres结果表](/cn.zh-CN/数据接入/实时写入/Flink/Blink独享/Hologres结果表.md)。

