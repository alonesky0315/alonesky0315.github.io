---
layout: post
title: 查看MySQL的sql运行记录
category: MySQL
keywords: Common MySQL
tags: Common MySQL
description: 
---

1.首先开启日志记录
```
SET GLOBAL log_output = 'TABLE';SET GLOBAL general_log = 'ON'
```

2.运行Sql语句

3.查看Sql运行日志（在命令行窗口可以查看具体的文本形式的Sql）

在navicat中打开命令行窗口：工具>命令列界面

运行以下Sql：
```
SELECT * from mysql.general_log ORDER BY event_time DESC
```

运行示例：
```
| 2023-07-24 08:50:54 | root[root] @ localhost [127.0.0.1] | 4 | 0 | Init DB | mysql
| 2023-07-24 08:50:54 | root[root] @ localhost [127.0.0.1] | 4 | 0 | Query | SHOW TABLE STATUS 
```

做完别忘记关掉日志记录（性能消耗大）：
```
SET GLOBAL log_output = 'TABLE';
SET GLOBAL general_log = 'OFF';
truncate table mysql.general_log;
```