---
layout: post
title: MySQL GROUP BY错误处理
category: MySQL
keywords: 
tags: 常识 MySQL
description: 
---

#### SQL语句
```
$list = model('order_package')
    ->field('id')
    ->where(['order_id'=>$order_id])
    ->order('pack', 'desc')
    ->group('pack')->select();
```
为查找出的发生碰撞的字段加上any_value函数

什么叫发生碰撞？比如我这里以create_date 做聚合，查出来的d_1有三个值，分别是1、2、3，这就是碰撞，因为聚合之后某一列有了多个值

##### 方式一
修改SQL语句:

MySQL大于MySQL5.7
```
$list = model('order_package')
    ->field('any_value(id)')
    ->where(['order_id'=>$order_id])
    ->order('pack', 'desc')
    ->group('pack')->select();
```
MySQL全版本
```
$list = model('order_package')
    ->field('GROUP_CONCAT(id)')
    ->where(['order_id'=>$order_id])
    ->order('pack', 'desc')
    ->group('pack')->select();
```
// 方式二
关闭mysql提供的安全检查ONLY_FULL_GROUP_BY模式。
通过`select @@sql_mode`得到的结果，去掉ONLY_FULL_GROUP_BY
或者执行SQL语句
```
SET GLOBAL sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,
ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```
// 方式三
通过更改my.cnf实现
```
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,
ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```