---
keyword: [Hologres, Python, Psycopg]
---

# Use Python to connect to Hologres

Psycopg is a PostgreSQL database adapter for the Python programming language. Hologres is compatible with PostgreSQL 11, which allows you to use Psycopg to connect to Hologres. This topic describes how to use Python to connect to Hologres. In this example, Python 3.8 and CentOS 7 are used.

## Install Python 3.8

You can install Python 3.8 in a Miniconda or Anaconda environment. In this example, Python 3.8 is installed on CentOS 7.

1.  Install Python 3.8.

    Download a version of Python based on actual needs and run the following commands to install Python:

    ```
    # yum install centos-release-scl
    # yum install rh-python38
    # scl enable rh-python38 bash
    # python --version
    Python 3.8.6
    ```

2.  Install the psycopg2 package.

    Run the following command to install the psycopg2 package:

    ```
     # pip install psycopg2-binary
    ```


## Connect to Hologres

After you install Python 3.8 and Psycopg, perform the following steps to connect to Hologres:

1.  Load the psycopg2 package.

    To use psycopg2, run the `import psycopg2` command to load the psycopg2 package that you installed.

2.  Connect to your Hologres database.

    You can use the `psycopg2.connect()` function to connect to your Hologres database. The following sample code provides an example on how to use this function. The following table describes the parameters of this function.

    ```
    conn = psycopg2.connect(host="<Endpoint>", port=<Port>, dbname="<databases>", user="<Access ID>", password="<Access Key>")
    ```

    |Parameter|Description|
    |---------|-----------|
    |host|The endpoint of your Hologres instance. You can view the endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
    |port|The port number of your Hologres instance. You can view the port number of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
    |dbname|The name of the Hologres database to which you want to connect.|
    |user|The AccessKey ID of your Alibaba Cloud account. You can obtain the AccessKey ID from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page. |
    |password|The AccessKey secret of your Alibaba Cloud account. You can obtain the AccessKey secret from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page. |


## Use Hologres

After you connect to the Hologres database, you can use psycopg2 to develop data in the database. You can perform the following steps to create a table, insert data, query the data, and then release resources:

1.  Create a cursor.

    Before you develop data, run the `cur = conn.cursor()` command to create a cursor for the connection.

2.  Develop data.

    1.  Create a table.

        The following sample command creates a table named `holo_test` and specifies that the type of data in the table is INTEGER. You can set a custom table name and specify a custom data type based on your business requirements.

        ```
        cur.execute("CREATE TABLE holo_test (num integer);")
        ```

    2.  Insert data.

        The following sample command inserts integers from 1 to 1,000 to the table named `holo_test`.

        ```
        cur.execute("INSERT INTO holo_test SELECT generate_series(%s, %s)", (1, 1000))
        ```

    3.  Query the data.

        ```
        cur.execute("SELECT sum(num) FROM holo_test;")
        cur.fetchone()
        ```

3.  Commit the transaction.

    After you write the command for data query, add the `conn.commit()` command to ensure that the operation is committed as a transaction.

4.  Release resources.

    After the preceding commands are run, run the following commands to close the cursor and disconnect from your Hologres database. This ensures that subsequent operations are not affected.

    ```
    cur.close()
    conn.close()
    ```


