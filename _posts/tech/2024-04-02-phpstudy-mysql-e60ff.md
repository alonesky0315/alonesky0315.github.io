---
layout: post
title: phpStudy 更换MySQL版本
category: Common
keywords: Common Tool
tags: Common Tool
description: 
---

1. 备份数据库
```
mysqldump --all-databases -u用户名 -p密码 > all_databases.sql
```
2. 停止MySQLa服务
```
sc stop MySQLa
```
3. 下载新版本MySQL [https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)
4. 移除phpstudy\MySQL下bin、lib、share、docs、include目录
5. 移除my.ini文件 table_cache=256、innodb_additional_mem_pool_size=2M
6. 升级数据库
```
mysql_upgrade -u用户名 -p密码
```
7.还原数据库(暂未找到验证语法)
```
暂无
```