# Common errors

This topic describes the common errors that you may encounter in using Hologres and how to fix them.

## Maximum number of partitions exceeded

**Scenario**: The following error message appears when you query data in a partitioned table:

`ERROR: AXF Exception: specified partitions count in odps table: <project_name.table_name> is: xxx, exceeds the limitation of xxx, please add stricter partition filter`

**Cause**: To guarantee the query performance, Hologres can scan a maximum of 50 partitions from a partitioned table during a single query.

**Workaround**: We recommend that you change the data table to be queried to a non-partitioned table or a partitioned table with fewer than 50 partitions before querying data in the table.

## Transaction error

**Scenario**: The following error message appears when you execute an SQL statement:

`ERROR: current transaction is aborted, commands ignored until end of transaction block`

**Cause**: A new SQL statement is executed when the previous SQL statement is still in progress.

**Workaround**: Run the `rollback` command.

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0584032951/p70124.png)

## I/O error

**Scenario**: The following error message appears when you execute an SQL statement:

`An I/O error occurred while sending to the backbend`

**Workaround**: Refresh the page and re-execute the SQL statement.

## DataService Studio or JDBC connection error

**Scenario**: The following error message appears when you connect to Hologres through DataWorks DataService Studio or Java Database Connectivity \(JDBC\):

`SQL state [XX000]; error code [0]; ERROR: Query could not generate plan by gporca : GPDB Expression type: Query Parameter not supported in DXL; nested exception is org.postgresql.util.PSQL`

**Cause**: Required extension parameters are absent in the connection string.

**Workaround**: Append the required extension parameters to the end of the connection string. The correct connection string is as follows:

`jdbc:postgresql://endpoint:port/dbname? preferQueryMode=simple&tcpKeepAlive=true`

## Error thrown during data query in a foreign table

**Scenario**: The following error message appears when you query MaxCompute data in a foreign table:

`ERROR: Query:[100181047458824706] Get result failed: exec sql failed => status:7 msg:ERROR: External table size 268911MB exceeded the maximum cache size 102400MB`

**Cause**: Hologres can scan a maximum of 100 GB data from a foreign table during a single query, but the foreign table contains more than 100 GB data.

**Workaround**:

1.  Specify filter conditions to filter out unnecessary data and make sure that the amount of data to be scanned does not exceed 100 GB.
2.  Import the MaxCompute data to Hologres and then query the imported data in Hologres. Hologres does not limit the amount of data to be scanned in itself.

