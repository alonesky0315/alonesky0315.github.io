---
layout: post
title: mysql bin 恢复_MySQL数据库恢复(使用mysqlbinlog命令)
category: mysql bin 恢复_MySQL数据库恢复(使用mysqlbinlog命令)
keywords: 
tags: 常识,MySQL
description: 
---

> mysql bin 恢复_MySQL数据库恢复(使用mysqlbinlog命令)

1：开启binlog日志记录修改mysql配置文件mysql.ini，在[mysqld]节点下添加

复制代码代码如下:

```
log-bin log-bin = E:/log/logbin.log
```

路径中不要包含中文和空格。重启mysql服务。通过命令行停止和启动mysql服务

复制代码代码如下:

```
c:\>net stop mysql; c:\>net start mysql;
```

进入命令行进入mysql并查看二进制日志是否已经启动Sql代码

复制代码代码如下:

```
mysql>show variables like 'log_%';
```

日志成功开启后，会在E:/log/目录下创建logbin.index和logbin.000001两个文件。logbin.000001就是数据库的备份文件，以后就可以通过此文件对数据库进行恢复操作。2：查看备份的二进制文件Sql代码

复制代码代码如下:

```
c:\mysql\bin\>mysqlbinlog e:/log/logbin.000001
```

日后记录的操作多了，命令行方式基本就用不上了。可以使用将日志导出文件的方式来查看日志内容2.1 导出Xml代码

复制代码代码如下:

```
c:\mysql\bin\>mysqlbinlog e:/log/logbin.000001 > e:/log/log.txt
```

">": 导入到文件中; ">>": 追加到文件中如果有多个日志文件Sql代码

复制代码代码如下:

```
c:\mysql\bin\> mysqlbinlog e:/log/logbin.000001 > e:/log/log.sql c:\mysql\bin\> mysqlbinlog e:/log/logbin.000002 >> e:/log/log.sql
```

2.2 按指定位置导出：Sql代码

复制代码代码如下:

```
c:\mysql\bin\>mysqlbinlog --start-position=185 --stop-position=338 e:/log/logbin.000001 > e:/log/log3.txt
```

2.3 按指定时间导出：Xml代码

复制代码代码如下:

```
c:\mysql\bin\>mysqlbinlog --start-datetime="2010-01-07 11:25:56" --stop-datetime="2010-01-07 13:23:50" e:/log/logbin.000001 > e:/log/log_by_date22.txt
```


3：从备份恢复数据库做了一次更新操作，之后日志的内容如下：Sql代码

复制代码代码如下:

```
/*!40019 SET @@session.max_insert_delayed_threads=0*/; /*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/; DELIMITER /*!*/; # at 4 #110107 13:23:50 server id 1 end_log_pos 106 Start: binlog v 4, server v 5.1.53-community-log created 110107 13:23:50 at startup # Warning: this binlog is either in use or was not closed properly. ROLLBACK/*!*/; BINLOG ' ZqMmTQ8BAAAAZgAAAGoAAAABAAQANS4xLjUzLWNvbW11bml0eS1sb2cAAAAAAAAAAAAAAAAAAAAA AAAAAAAAAAAAAAAAAABmoyZNEzgNAAgAEgAEBAQEEgAAUwAEGggAAAAICAgC '/*!*/; # at 106 #110107 13:26:58 server id 1 end_log_pos 185 Query thread_id=44 exec_time=1 error_code=0 SET TIMESTAMP=1294378018/*!*/; SET @@session.pseudo_thread_id=44/*!*/; SET @@session.foreign_key_checks=1, @@session.sql_auto_is_null=1, @@session.unique_checks=1, @@session.autocommit=1/*!*/; SET @@session.sql_mode=1344274432/*!*/; SET @@session.auto_increment_increment=1, @@session.auto_increment_offset=1/*!*/; /*!\C utf8 *//*!*/; SET @@session.character_set_client=33,@@session.collation_connection=33,@@session.collation_server=33/*!*/; SET @@session.lc_time_names=0/*!*/; SET @@session.collation_database=DEFAULT/*!*/; BEGIN /*!*/; # at 185 #110107 13:26:58 server id 1 end_log_pos 338 Query thread_id=44 exec_time=1 error_code=0 use ncl-interactive/*!*/; SET TIMESTAMP=1294378018/*!*/; UPDATE `t_system_id` SET `id_value`='3000' WHERE (`table_name`='t_working_day') /*!*/; # at 338 #110107 13:26:58 server id 1 end_log_pos 365 Xid = 8016 COMMIT/*!*/; DELIMITER ; DELIMITER /*!*/; DELIMITER ; # End of log file ROLLBACK /* added by mysqlbinlog */; /*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/; /*!40019 SET @@session.max_insert_delayed_threads=0*/; /*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/; DELIMITER /*!*/; # at 4 #110107 13:23:50 server id 1 end_log_pos 106 Start: binlog v 4, server v 5.1.53-community-log created 110107 13:23:50 at startup # Warning: this binlog is either in use or was not closed properly. ROLLBACK/*!*/; BINLOG ' ZqMmTQ8BAAAAZgAAAGoAAAABAAQANS4xLjUzLWNvbW11bml0eS1sb2cAAAAAAAAAAAAAAAAAAAAA AAAAAAAAAAAAAAAAAABmoyZNEzgNAAgAEgAEBAQEEgAAUwAEGggAAAAICAgC '/*!*/; # at 106 #110107 13:26:58 server id 1 end_log_pos 185 Query thread_id=44 exec_time=1 error_code=0 SET TIMESTAMP=1294378018/*!*/; SET @@session.pseudo_thread_id=44/*!*/; SET @@session.foreign_key_checks=1, @@session.sql_auto_is_null=1, @@session.unique_checks=1, @@session.autocommit=1/*!*/; SET @@session.sql_mode=1344274432/*!*/; SET @@session.auto_increment_increment=1, @@session.auto_increment_offset=1/*!*/; /*!\C utf8 *//*!*/; SET @@session.character_set_client=33,@@session.collation_connection=33,@@session.collation_server=33/*!*/; SET @@session.lc_time_names=0/*!*/; SET @@session.collation_database=DEFAULT/*!*/; BEGIN /*!*/; # at 185 #110107 13:26:58 server id 1 end_log_pos 338 Query thread_id=44 exec_time=1 error_code=0 use ncl-interactive/*!*/; SET TIMESTAMP=1294378018/*!*/; UPDATE `t_system_id` SET `id_value`='3000' WHERE (`table_name`='t_working_day') /*!*/; # at 338 #110107 13:26:58 server id 1 end_log_pos 365 Xid = 8016 COMMIT/*!*/; DELIMITER ; DELIMITER /*!*/; DELIMITER ; # End of log file ROLLBACK /* added by mysqlbinlog */; /*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
```

3.1 恢复：Sql代码

复制代码代码如下:

```
c:\mysql\bin\>mysqlbinlog e:/log/logbin.000001 | mysql -u root -p
```

3.2 按指定位置恢复：Sql代码

复制代码代码如下:

```
c:\mysql\bin\>mysqlbinlog --start-position=185 --stop-position=338 e:/log/logbin.000001 | mysql-u root -p
```

3.3 按指定时间恢复：Xml代码

复制代码代码如下:

```
c:\mysql\bin\>mysqlbinlog --start-datetime="2010-01-07 11:25:56" --stop-datetime="2010-01-07 13:23:50" e:/log/logbin.000001 | mysql -u root -p
```

3.4 通过导出的脚本文件恢复Sql代码

复制代码代码如下:

```
c:\mysql\bin\>mysql -e "source e:/log/log.sql"
```

4.其他常用操作4.1 查看所有日志文件Sql代码

复制代码代码如下:

```
mysql>show master logs;
```

4.2 当前使用的binlog文件Sql代码

复制代码代码如下:

```
mysql>show binlog events \g;
```

4.3 产生一个新的binlog日志文件Sql代码

复制代码代码如下:

```
mysql>flush logs;
```

4.4 删除所有二进制日志，并从新开始记录(注意：reset master命令会删除所有的二进制日志)Sql代码

复制代码代码如下:

```
mysql > flush logs; mysql > reset master;
```

4.5 快速备份数据到sql文件Sql代码

复制代码代码如下:

```
c:\mysql\bin>mysqldump -u root -p --opt --quick interactive > e:/log/mysqldump.sql
```

为了方便查看，把从脚本恢复的命令在写一次Sql代码

复制代码代码如下:

```
c:\mysql\bin\>mysql -e "source e:/log/mysqldump.sql"
```

原文链接：[https://blog.csdn.net/weixin_31951239/article/details/113589799](https://blog.csdn.net/weixin_31951239/article/details/113589799)