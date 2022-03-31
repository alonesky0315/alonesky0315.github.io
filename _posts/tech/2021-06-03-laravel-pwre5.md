---
layout: post
title: Laravel获取不同目录文件夹路径的函数
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

#### 
laravel下面有几个取特殊路径的函数，这里做个总结。大家按需取用即可。除了base_path是指的代码根目录外，其他的几个函数都指代的是具体的同名目录。
```
base_path()    //站点根目录
app_path()     //app目录
public_path()  //public目录
storage_path()  // storage 目录
resource_path()  //resources 目录
config_path()    // config 目录
database_path()  // database 目录
各个资源路径常量
一、public_path('uploads');
说明：public文件路径
二、base_path('xx');
三、app_path('xx');
四、resource_path('xx');
```

参考链接: [https://www.cnblogs.com/haoxuanchen2014/p/9636473.html](https://www.cnblogs.com/haoxuanchen2014/p/9636473.html)