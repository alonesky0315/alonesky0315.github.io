---
layout: post
title: mysql导入数据时常见错误
category: 技术
keywords: 技术
tags: 技术,mysql,导入数据
description: mysql导入数据时常见错误
---
#### mysql导入数据时常见错误
一、提示 USING BTREE 错误解决办法   
错误原因：    
    mysql 5.1和mysql 5.0在处理到索引语句时有所区别。   
案例： 
    有时导入mysql会提示如下错误： 
```
1064 - You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'USING BTREE
) ENGINE=MyISAM DEFAULT CHARSET=utf8' at line 7　　
```
解决办法：   
(1) 打开要导入的文件在里面搜索 BTREE 找到如下内容 
```
KEY pkey (pkey) USING BTREE # mysql5.1　　
```　　
修改为 　　
```
KEY pkey USING BTREE　(pkey) 　# mysql5.0
```　
(2)找一个数据库结构一样的数据库导出结构然后导入到需要的数据库然后挨个导入数据
二、提示 doesn't have a default value 错误解决办法
sql-mode 有 STRICT_TRANS_TABLES（严格模式）,在mysql 5.7之后，数据库默认都是采用严格模式


