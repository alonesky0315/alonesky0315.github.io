---
layout: post
title: Laravel常用目录路径获取
category: PHP
keywords: 
tags: 
description: 
---

1.app目录的绝对路径
```
// app目录的绝对路径
$path = app_path();
// 相对于app目录的指定文件生成绝对路径
$path = app_path('Http/Controllers/Controller.php');
```
2.项目根目录的绝对路径
```
// 项目根目录的绝对路径
$path = base_path();
// 相对于应用目录的指定文件生成绝对路径
$path = base_path('vendor/bin');
```
3.应用配置目录的绝对路径
```
$path = config_path();
```
4.应用数据库目录的绝对路径
```
$path = database_path();
```
5.public目录的绝对路径
```
$path = public_path();
```
6.storage目录的绝对路径
```
// storage目录的绝对路径
$path = storage_path();
// 生成相对于storage目录的指定文件的绝对路径
$path = storage_path('app/file.txt');
```
参考链接：[https://www.cnblogs.com/www-php/p/9242980.html](https://www.cnblogs.com/www-php/p/9242980.html)