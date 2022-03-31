---
layout: post
title: PHP Redis基本操作
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

#### php 判断redis是否连接成功
```
//连接本地的 Redis 服务
$redis = new Redis();
//连接redis     地址    端口   连接超时时间  连接成功返回true  失败返回false
$redis->connect('127.0.0.1', 6379,30);
//连接redis密码认证，成功返回true 失败返回false
$ret = $redis->auth('password');
//选择数据库
$redis->select(3);
//查看连接状态 连接正常返回+PONG
echo $redis->ping();
//设置测试key
$redis->set( "name" , "小明"); 
//输出key value
echo $redis->get("name");
```