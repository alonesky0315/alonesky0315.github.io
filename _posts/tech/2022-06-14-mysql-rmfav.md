---
layout: post
title: MySQL数据库优化
category: MySQL
keywords: 
tags: 常识 MySQL
description: 
---

1. 查询数据库所占空间

```
SELECT
    concat(round(sum(data_length / 1024 / 1024),2),'MB') AS data_size
FROM
    information_schema.TABLES
WHERE
    table_schema = 'dbName';
```

2. 查询表所占空间

```
SELECT
    concat(round(sum(data_length / 1024 / 1024),2),'MB') AS data_size
FROM
    information_schema.TABLES
WHERE
    table_schema = 'dbName' AND table_name = 'tableName';
```

3. 查询数据库所有表所占空间

```
SELECT
    concat(round(sum(data_length / 1024 / 1024),2),'MB') AS data_size, table_name
FROM
    information_schema.TABLES
WHERE
    table_schema = 'dbName'
GROUP BY
    table_name
ORDER BY
    data_size DESC;
```

4. 查看所有数据库容量大小

```
SELECT
    table_schema AS '数据库名称',
    sum(table_rows) AS '记录数',
    sum(
        TRUNCATE (data_length / 1024 / 1024, 2)
    ) AS '数据容量(MB)',
    sum(
        TRUNCATE (index_length / 1024 / 1024, 2)
    ) AS '索引容量(MB)'
FROM
    information_schema.TABLES
GROUP BY
    table_schema
ORDER BY sum(data_length) DESC, sum(index_length) DESC;
```

5. 查看所有数据库各表容量大小

```
SELECT
    table_schema AS '数据库名称',
    table_name AS '表名',
    table_rows AS '记录数',
    TRUNCATE (data_length / 1024 / 1024, 2) AS '数据容量(MB)',
    TRUNCATE (index_length / 1024 / 1024, 2) AS '索引容量(MB)'
FROM
    information_schema.TABLES
ORDER BY data_length DESC,index_length DESC;
```

6. 查看指定数据库容量大小

```
SELECT
    table_schema AS '数据库名称',
    sum(table_rows) AS '记录数',
    sum(
        TRUNCATE (data_length / 1024 / 1024, 2)
    ) AS '数据容量(MB)',
    sum(
        TRUNCATE (index_length / 1024 / 1024, 2)
    ) AS '索引容量(MB)'
FROM
    information_schema.TABLES
WHERE
    table_schema = 'dbName';
```

7. 查看指定数据库各表容量大小

```
SELECT
    table_schema AS '数据库名称',
    table_name AS '表名',
    table_rows AS '记录数',
    TRUNCATE (data_length / 1024 / 1024, 2) AS '数据容量(MB)',
    TRUNCATE (index_length / 1024 / 1024, 2) AS '索引容量(MB)'
FROM
    information_schema.TABLES
WHERE
    table_schema = 'wxapp_hntianjin_com'
ORDER BY data_length DESC, index_length DESC;
```

8. mysql数据库整理碎片方法：    

1) 查看碎片方法
   
```
SELECT table_schema db, table_name, data_free,engine
FROM
    information_schema.TABLES
WHERE
    table_schema NOT IN (
        'information_schema',
        'mysql',
        'sys'
    )
AND data_free > 0;
```

1) 清除表碎片    

```    
// InnoDB表 
alter table tableName engine=InnoDB;
// MyISAM表
optimize table tableName;
```

参考链接：

MySQL数据库查看表占用空间大小及碎片整理：[https://blog.csdn.net/mariopq/article/details/123864589](https://blog.csdn.net/mariopq/article/details/123864589)

mysql 表空间碎片_MySQL碎片产生的原因及清除表空间碎片的方法：[https://blog.csdn.net/weixin_29044157/article/details/113310426](https://blog.csdn.net/weixin_29044157/article/details/113310426)