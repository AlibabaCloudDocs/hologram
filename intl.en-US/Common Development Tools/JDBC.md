---
keyword: [JDBC, Hologres]
---

# JDBC

Hologres provides the Java Database Connectivity \(JDBC\) and Open Database Connectivity \(ODBC\) interfaces that are fully compatible with PostgreSQL. You can connect an SQL client to Hologres by using these interfaces. This topic describes how to use JDBC to connect to Hologres for data development.

1.  Download the JDBC driver.

    -   If client tools integrate PostgreSQL Database Driver, you can directly use their built-in driver. If PostgreSQL Database Driver is not integrated, download [PostgreSQL JDBC Driver](https://mvnrepository.com/artifact/org.postgresql/postgresql) from the Maven repository. We recommend that you download PostgreSQL JDBC Driver V42.2.18 or later. The following sample code is for your reference:

        ```
        <dependencies>
                <dependency>
                    <groupId>org.postgresql</groupId>
                    <artifactId>postgresql</artifactId>
                    <version>42.2.18.jre6</version> 
                                                     
                </dependency>
            </dependencies>
        ```

    -   You can also use a driver that is specially developed for Hologres. The following sample code is for your reference:

        ```
        <dependency>
          <groupId>com.alibaba.hologres</groupId>
          <artifactId>postgresql-holo</artifactId>
          <version>42.2.18.4</version>
        </dependency>
        ```

        In addition to the features of PostgreSQL JDBC Driver, this driver provides the following features:

        -   You can write data to a child partitioned table by using a parent partitioned table.
        -   When a string in the format of yyyyMMddHHmmss is written to a column of the TIMESTAMP type, the string is automatically converted to a timestamp.
        -   The parameters that are described in the following table are added.

            |Parameter|Description|Default value|
            |---------|-----------|-------------|
            |dynamicPartition|Specifies whether to automatically create a child partitioned table if the child partitioned table to which you want to write data does not exist.|false|
            |inputNumberAsEpochMsForDatetimeColumn|Specifies whether to treat the data of the LONG type that is written to a column of the TIMESTAMP type as the number of milliseconds that have elapsed since January 1, 1970, 00:00:00 UTC.|false|
            |inputStringAsEpochMsForDatetimeColumn|Specifies whether to treat the data of the STRING type that is written to a column of the TIMESTAMP type as the number of milliseconds that have elapsed since January 1, 1970, 00:00:00 UTC.|false|

2.  Use JDBC to connect to Hologres.

    The following sample code is for your reference:

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

    For more information about the configuration of JDBC, visit [Java Concept Of The Day](https://javaconceptoftheday.com/difference-between-executequery-executeupdate-execute-in-jdbc/).

3.  Analyze data.

    After you connect to Hologres by using JDBC, you can use standard SQL statements to analyze data. The following list provides some typical data development scenarios:

    -   Accelerated query of offline data: For more information, see [Create a foreign table in Hologres to accelerate queries of MaxCompute data](/intl.en-US/Data Access/Batch synchronization/MaxCompute/Create a foreign table in Hologres to accelerate queries of MaxCompute data.md).

