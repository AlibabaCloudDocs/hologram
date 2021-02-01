# JDBC

Hologres provides the Java Database Connectivity \(JDBC\) or Open Database Connectivity \(ODBC\) driver. You can use JDBC to connect to Hologres by following these steps:

1.  **Download the JDBC driver.**

    Download [PostgreSQL JDBC Driver](https://mvnrepository.com/artifact/org.postgresql/postgresql) from the Maven repository. We recommend that you download V42.2.xx or a later version.

    ```
    <dependencies>
            <dependency>
                <groupId>org.postgresql</groupId>
                <artifactId>postgresql</artifactId>
                <version>42.2.8.jre6</version> 
    
            </dependency>
        </dependencies>
    ```

2.  **Use JDBC to connect to Hologres.**

    ```
    public class HologresTest {
    
        private void jdbcExample() throws Exception {
            String url = "jdbc:postgresql://{ENDPOINT}:{PORT}/{DBNAME}? user={ACCESS_ID}&password={ACCESS_KEY}";
            Connection conn = DriverManager.getConnection(url);
            Statement st = conn.createStatement();
            String sql = "SELECT * FROM table where xxx limit 100";
            ResultSet rs = st.executeQuery(sql);
            String c1 = rs.getString(1);
        }
    }
    ```

3.  **Analyze data.**

    After you connect to Hologres by using JDBC, you can use standard SQL statements to analyze data. The typical data analytics scenarios include:

    -   Accelerated query of offline data: [Create a foreign table to accelerate queries of MaxCompute data](/intl.en-US/Data Access/Big Data/MaxCompute/Create a foreign table to accelerate queries of MaxCompute data.md)
    -   Real-time data writing: [t1862972.md\#](/intl.en-US/Data Access/Big Data/Realtime Compute/Write data from Realtime Compute to Hologres in real time.md)

