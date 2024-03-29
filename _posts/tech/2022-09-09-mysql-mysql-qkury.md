---
layout: post
title: mysql备份命令_mysql命令行备份方法
category: MySQL
keywords: 
tags: 常识 MySQL
description: 
---

一、 mysql备份

1、备份命令

格式：mysqldump -h主机IP -P端口 -u用户名 -p密码 --database 数据库名 > 文件名.sql

#本地备份可以不添加端口和主机IP，username、passward是数据库用户名和密码

```
mysqldump -h IP -P 3306 -u username -p database > /data/backup/mysql.sql
mysqldump -h IP -P 3306 -u username -p password --database mysql > /data/backup/mysql.sql
```

2、 备份压缩

格式：mysqldump -h主机IP -P端口 -u用户名 -p密码 --database 数据库名 | gzip > 文件名.sql.gz

#导出的数据有可能比较大，不好备份到远程，这时候就需要进行压缩

```
mysqldump -h IP -p 3306 -u username -p password --database mysql | gzip > /data/backup/mysql.sql.gz
```

3、 备份同个库多个表

格式：mysqldump -h主机IP -P端口 -u用户名 -p密码 --database 数据库名 表1 表2 .... > 文件名.sql

```
mysqldump -h IP -p 3306 -u username -p password mysql1 mysql2 mysql3> /data/backup/mysql_db.sql
```

4、 同时备份多个库

格式：mysqldump -h主机IP -P端口 -u用户名 -p密码 --databases 数据库名1 数据库名2 数据库名3 > 文件名.sql

```
mysqldump -h IP -p 3306 -u username -p password --databases mysql1 mysql2 mysql3 > /data/backup/mysql_db.sql
```

5、 备份实例上所有的数据库

格式：mysqldump -h主机IP -P端口 -u用户名 -p密码 --all-databases > 文件名.sql

```
mysqldump -h IP -p 3306 -u username -p password --all-databases > /data/backup/mysql_db.sql
```

6、 备份数据出带删除数据库或者表的sql备份

格式：mysqldump -h主机IP -P端口 -u用户名 -p密码 --add-drop-table --add-drop-database 数据库名 > 文件名.sql

```
mysqldump -h IP -p 3306 -u username -p password --add-drop-table --add-drop-database mysql > /data/backup/mysql_db.sql
```

7、 备份数据库结构，不备份数据

格式：mysqldump -h主机IP -P端口 -u用户名 -p密码 --no-data 数据库名1 数据库名2 数据库名3 > 文件名.sql

```
mysqldump -h IP -p 3306 -u username -p password --no-data –databases mysql1 mysql2 mysql3 > /data/backup/structure_db.sql
```

8、 还原MySQL数据库的命令

#database为数据库名

```
mysql -h IP -u username -p password database < backupfile.sql
```

9、 还原压缩的MySQL数据库

#database为数据库名

```
gunzip < backupfile.sql.gz | mysql -u username -p password database
```

10、 将数据库转移到新服务器

#database为数据库名

```
mysqldump -u username -p password database | mysql –host=IP -C database
```

11、 导入数据库

常用source命令，用use进入到某个数据库
```
mysql>source d:\test.sql
```
后面的参数为脚本文件。

12、 查看binlog日志

查看binlog日志可用命令 
```
mysqlbinlog  binlog日志名称|more
```

13、 general_log

General_log记录数据库的任何操作，查看general_log 的状态和位置可以用命令
```
show variables like "general_log%"
```
开启general_log可以用命令
```
set global general_log=on
```

14、 --master-data 和--single-transaction

在mysqldump中使用--master-data=2，会记录binlog文件和position的信息;

--single-transaction会将隔离级别设置成repeatable-commited。

二、 增量备份

1、 首先做一次完整备份：

#这时候就会得到一个全备文件test.sql

```
mysqldump-h IP -u username -p passward -p 3310 --single-transaction --master-data=2 test>test.sql
```

在sql文件中我们会看到：

--master-data=2是指备份后所有的更改将会保存到bin-log.000002二进制文件中。

```
CHANGE MASTER TO MASTER_LOG_FILE='bin-log.000002', MASTER_LOG_POS=107;
```

2、 在test库的t_student表中增加两条记录，然后执行flush logs命令。

这时将会产生一个新的二进制日志文件bin-log.000003，bin-log.000002则保存了全备过后的所有更改，既增加记录的操作也保存在了bin-log.00002中。

3、 在test库中的a表中增加两条记录，然后误删除t_student表和a表。

a中增加记录的操作和删除表a和t_student的操作都记录在bin-log.000003中。

三、 恢复

1、 首先导入全备数据

#也可以直接在mysql命令行下面用source导入

```
mysql-h IP -u username -p passward -p 3310 < test.sql
```

2、 恢复bin-log.000002

```
mysqlbinlog bin-log.000002 |mysql -h IP -u username -p passward -p 3310
```

3、 恢复部分 bin-log.000003

在general_log中找到误删除的时间点，然后更加对应的时间点到bin-log.000003中找到相应的position点，需要恢复到误删除的前面一个position点。

可以用如下参数来控制binlog的区间

--start-position 开始点 --stop-position 结束点

--start-date 开始时间  --stop-date  结束时间

找到恢复点后，既可以开始恢复。

```
mysqlbinlog mysql-bin.000003 --stop-position=208 |mysql -hIP -u username -p passward -p 3310
```

原文链接：[https://blog.csdn.net/weixin_28935113/article/details/113114410]((https://blog.csdn.net/weixin_28935113/article/details/113114410)