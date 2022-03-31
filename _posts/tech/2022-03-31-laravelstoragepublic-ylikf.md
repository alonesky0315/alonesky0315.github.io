---
layout: post
title: Laravel建立storage目录文件到public的软连接
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

在写接口上传的照片如何保存到public让前端框架访问到，就要建立软连接将照片放到public目录去访问！ 很简单

执行命令：
```
php artisan storage:link
```

命令执行完毕后，就会在项目里多出一个 public/storage，

这个 storage 就是一个软链接，它指向 storage/app/public 目录。

public/storage（软连接） → storage/app/public

然后就可以用地址直接访问public里面的照片了！

原文链接：[https://blog.csdn.net/cfun_goodmorning/article/details/79085626](https://blog.csdn.net/cfun_goodmorning/article/details/79085626)