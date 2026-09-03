---
layout: post
title: MySQL自带全文索引（FULLTEXT）
category: MySQL
keywords: Common MySQL
tags: Common MySQL
description: 
---

#### MySQL自带全文索引（FULLTEXT）

1. 创建表
```sql
CREATE TABLE `demo` (
	`id` int(10) NOT NULL AUTO_INCREMENT PRIMARY KEY,
	`title` varchar(20) DEFAULT NULL COMMENT '标题',
	FULLTEXT(title) WITH PARSER ngram COMMENT 'ngram_token_size=2'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='测试表'
```
2. 插入数据
```sql
INSERT INTO `demo` (`title`) VALUES ('旺旺饼干');
INSERT INTO `demo` (`title`) VALUES ('鸡蛋饼干');
INSERT INTO `demo` (`title`) VALUES ('奶油饼干');
INSERT INTO `demo` (`title`) VALUES ('旺旺仙贝');
INSERT INTO `demo` (`title`) VALUES ('旺旺雪饼');
```
3. 查询
  * 在布尔模式中，+ 符号表示该词必须存在, 但可以省略
  * 在布尔模式中，- 符号表示该词必须不存在，但**必须配合至少一个肯定条件使用**

  ```sql
	
  // 同时包含旺旺和饼干的记录
  SELECT * FROM demo WHERE MATCH(title) AGAINST('+旺旺 +饼干' IN BOOLEAN MODE);
	
  // 包含旺旺或饼干的记录
  SELECT * FROM demo WHERE MATCH(title) AGAINST('旺旺 饼干' IN BOOLEAN MODE);
	
  // 包含旺旺但不包含饼干的记录
  SELECT * FROM demo WHERE MATCH(title) AGAINST('+旺旺 -饼干' IN BOOLEAN MODE);
	
  // 不包含旺旺的所有记录
  SELECT * FROM demo WHERE MATCH(title) AGAINST('+* -旺旺' IN BOOLEAN MODE);
	
  ```