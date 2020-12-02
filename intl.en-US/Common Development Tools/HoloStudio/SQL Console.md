---
keyword: [SQL Console, Hologres]
---

# SQL Console

The SQL Console module of HoloStudio allows you to analyze data in Hologres by executing SQL statements. Query results can be obtained in seconds. This topic describes the features and usage of SQL Console.

## Folder

The Folder module stores new ad hoc queries.

In the left-side navigation pane, click **SQL Console**. Move the pointer over the **Create** icon and click **Folder**. In the dialog box that appears, create a folder. You can create an ad hoc query in the folder and execute standard SQL statements on tables. You can also right-click a table in the folder and select the relevant menus to move, rename, or delete the table.

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3057552951/p58667.png)

## SQL Console

The SQL Console module can generate an SQL editor, in which you can run standard SQL statements. To use the service, you must create a folder first. If you have already created a folder, perform the following steps:

1.  In the left-side navigation pane, click **SQL Console**. Move the pointer over the Create icon and click SQL Console. In the dialog box that appears, set the required parameters.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3057552951/p58668.png)

    |Parameter|Description|
    |---------|-----------|
    |Node Name|The name of the ad hoc query.|
    |Location|The folder where the ad hoc query is stored.|
    |Database|The database to which the ad hoc query applies.|

2.  Write the SQL statements used in the ad hoc query and click **Run** to run the query. Then, you can check the query result. The following example shows how to create a table, import data to the table, and query the table:

    ```
    CREATE TABLE supplier (
     s_suppkey bigint NOT NULL,
     s_name text NOT NULL,
     s_address text NOT NULL,
     s_nationkey bigint NOT NULL,
     s_phone text NOT NULL,
     s_acctbal bigint NOT NULL,
     s_comment text NOT NULL,
    PRIMARY KEY (s_suppkey)
    );
    
    INSERT INTO supplier VALUES 
    (1, 'Supplier#000000001', 'gf0JBoQDd7tgrzrddZ', 17, '27-918-335-1736',  575594, 'each slyly above the careful'),  
    (6, 'Supplier#000000006', 'tQxuVm7s7CnK', 14, '24-696-997-4969',  136579, 'final accounts. regular dolphins use against the furiously ironic decoys. '),
    (10, 'Supplier#000000010',  'Saygah3gYWMp72i PY', 24, '34-852-489-8585', 389191, 'ing waters. regular requests ar'),  
    (18, 'Supplier#00000001', 'PGGVE5PWAMwKDZw', 16, '26-729-551-1115', 704082, 'accounts snooze slyly furiously bold'),  
    (39, 'Supplier#000000039', 'SYpEPWr1yAFHaC91qjFcijjeU5eH',  8, '18-851-856-5633 611565', 88990, 'le slyly requests. special packages shall are blithely. slyly unusual packages sleep'),  
    (48, 'Supplier#000000048',  'FNPMQDuyuKvTnLXXaLf3Wl6OtONA6mQlWJ', 14, '24-722-551-9498',563062, 'xpress instructions affix. fluffily even requests boos');  
    
    SELECT * FROM supplier;
    ```

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3057552951/p66692.png)

    |Element|Description|
    |-------|-----------|
    |SQL editor|You can write SQL statements in the SQL editor.|
    |Save icon|You can click this icon to save all statements in the SQL editor.|
    |Run icon|You can click this icon to execute all statements in the SQL editor. The result appears at the bottom. You can also select an SQL statement to be executed. In this case, the system executes only this statement. **Note:** If not selected, all statements in the edit box are executed by default. |
    |Reload icon|You can click this icon to refresh the content in the SQL editor. The system retains only the saved content after the refresh.|
    |Stop icon|You can click this icon to stop executing SQL statements.|
    |Operational Log|Check the execution status of SQL statements. You can check the execution results and error messages.|
    |Result|You can check the table content after the SQL statements are executed.|

    **Note:** If no result is returned to SQL statements such as the `CREATE TABLE` statement, only operational logs are generated after the statements are executed.

    HoloStudio also allows you to copy, query, and hide columns.


## Video demo

The following video shows how to use SQL Console. 

