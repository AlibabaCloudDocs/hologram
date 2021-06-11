---
keyword: [自增序列, Serial, Bigserial]
---

# 自增序列Serial

交互式分析Hologres兼容Postgres生态，支持在建表时将字段的类型定义为Serial和Bigserial来定义自增的字段。

## 数据类型简介

Hologres在建表时可以将字段的数据类型定义为Serial和Bigserial。其中，Serial表示INT4类型的自增字段，Bigserial表示INT8类型的自增字段。

配置Serial类型的自增序列。如下以Serial类型为例，对表格中的colname字段实现自增，Bigserial类型同样适用。

```
CREATE TABLE tablename (
    colname serial
);
```

## 使用限制

-   Hologres不能指定Serial和Bigserial的额外参数，包括递增步长及初始值参数，递增步长为1，初始值默认设置为1。
-   Hologres暂不支持Smallserial类型的数据。
-   首次创建Serial和Bigserial类型的表，需要Superuser在当前DB创建。例如Superuser执行`create table test(a serial);` ，然后其余用户就能按照业务逻辑创建有关Seria和Bigseriall类型的表。创建Serial和Bigserial类型的表是DB级别的，如果切换数据库， Superuser需要再次执行该操作。
-   当前Flink和数据集成SDK模式暂不支持写入Serial和Bigserial类型，仅支持使用`insert into xxx select`方式写入Serial和Bigserial类型。
-   仅Hologres V0.10及以上版本支持修改Serial参数，且仅支持`restart with`参数的修改。

## 示例一：使用SQL语句实现自增序列

如下以Serial类型为例，使用SQL语句实现自增序列，Bigserial类型同样适用。

```
--创建表，包含id和f1两个字段。
create table if not exists test_tb(id serial primary key, f1 text);

--在insert语句中，给f1字段插入数据。
insert into test_tb(f1) values('1');
insert into test_tb(f1) values('2');
insert into test_tb(f1) values('3');

--查询test_tb表，按照id增序排列。
select * from test_tb order by id asc;
```

## 示例二：使用JDBC连接Hologres，实现自增序列

如下以Serial类型为例，使用JDBC连接Hologres实现自增序列，Bigserial类型同样适用。

```
package test;

import java.sql.*;

public class HoloSerial {

//创建表，包含id和f1两个字段。
    private static void Init(Connection conn) throws Exception {
        try (Statement stmt = conn.createStatement()) {
            stmt.execute("drop table if exists test_tb;");
            stmt.execute("create table if not exists test_tb(id serial primary key, f1 text);");
        }
    }

//给f1字段插入数据。
    private static void TestSerial(Connection conn) throws Exception {
        try (PreparedStatement stmt = conn.prepareStatement("insert into test_tb(f1) values(?)")) {
            for (int i = 0; i < 100; ++i) {
                stmt.setString(1, String.valueOf(i + 1));
                int affected_rows = stmt.executeUpdate();
                System.out.println("affected rows => " + affected_rows);
            }
        }

//查询test_tb表，按照id增序排列。
        try (PreparedStatement stmt = conn.prepareStatement("select * from test_tb order by id asc")) {
            try(ResultSet rs = stmt.executeQuery()) {
                while(rs.next()) {
                    String res = rs.getObject(1).toString() + "\t" + rs.getObject(2).toString();
                    System.out.println(res);
                }
            }
        }
    }

//使用JDBC连接Hologres。
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

## 示例三：修改Serial参数

当您在创建表时使用Serial参数时，创建完成会自动生成一个名为**schema\_name.tablename\_columnname\_seq**的Sequence，您可以通过修改Sequence参数控制Serial的生成方式。具体操作方法如下所示：

1.  执行如下代码查看生成的Sequence，其中，table\_schematable\_name和column\_name的取值需要修改为实际的名称。本示例以示例一创建的表信息为例进行说明。

    ```
    select table_name,column_name,column_default  from information_schema.columns where table_schema='ods' and table_name = 'test_tb' and column_name = 'id';
    ```

    查询结果如下图所示：

    ![查询结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4967933261/p283145.png)

    查询结果中，引号部分的`ods.test_tb_id_seq`为Sequence名称。

2.  在获取到Sequence名称之后，您可以执行如下示例语句，对Serial的`restart with`参数进行修改。

    ```
    alter sequence ods.test_tb_id_seq restart with 100
    ```

    其中，`ods.test_tb_id_seq`名称和具体数量您可以按照实际业务内容进行修改。修改完成后，您可以给表中插入数据进行验证。


