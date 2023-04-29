---
layout: post
title: Laravel 报错 “No application encryption key has been specified” 处理方法
category: 知识库
keywords: 
tags: 常识 PHP
description: 
---

> 出现该报错是因为没有设置应用程序加密密钥（配置文件 .env 中的APP_KEY），如果应用密钥还没有设置，你的用户会话和其他的加密数据将会不安全！

#### 处理方法

1、检查Laravel项目配置文件.env是否存在,若配置文件不存在,可复制.env.example并重命名为.env

2、在项目根目录下执行命令生成key,该命令会生成APP_KEY并写入到.env文件中

```
php artisan key:generate
```

3、若APP_KEY生成后仍然报错“No application encryption key has been specified”，则是laravel应用缓存导致，执行清理应用缓存命令；清除完缓存，执行重新配置缓存命令

```
// 清理应用缓存
php artisan cache:clear 

// 重新配置缓存
 php artisan config:cache 
```

原文链接：[https://blog.csdn.net/weixin_43675941/article/details/127785643](https://blog.csdn.net/weixin_43675941/article/details/127785643)