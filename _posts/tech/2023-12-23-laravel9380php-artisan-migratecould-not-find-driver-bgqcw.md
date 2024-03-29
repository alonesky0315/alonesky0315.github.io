---
layout: post
title: laravel9.38.0中执行php artisan migrate提示could not find driver
category: PHP
keywords: Common PHP MySQL
tags: Common PHP MySQL
description: 
---

运行php artisan migrate导入数据库到mysql时，报了错误：提示could not find driver

![image.png](https://blog.alonesky.com/storage/article/2023/12/23/mlay4d0ekv7LWjtui9p2y6bmkdjv3oUJRN1uWUnG.png)

这种问题实际上是php中没有配置mysql插件导致的。php连接mysql的驱动程序有两种：MySQLi 和 PDO，你只需要在配置文件php.ini中将它启用，由于上面的报错是没有启用PDO扩展导致的，这里将“;extension=pdo_mysql”一行前面的分号去掉即可。

![image.png](https://blog.alonesky.com/storage/article/2023/12/23/85ZUtzjghtBf6LaPeJrLpwj0oXf68gVPhowgL8sF.png)

原文链接：[https://blog.csdn.net/qiuchangyong/article/details/128027473](https://blog.csdn.net/qiuchangyong/article/details/128027473)