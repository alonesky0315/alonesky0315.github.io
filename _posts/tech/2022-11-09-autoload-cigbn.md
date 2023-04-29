---
layout: post
title: 使用autoload创建辅助函数文件
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

> 在Laravel和ThinkPHP框架中有时需要自定义辅助函数
> 所以这里我们可以通过Composer自带的autoload进行自动载入

在app的Common文件夹下面创建了一个Helpers.php文件（该文件也可以放在其它地方）

app/Common/Helpers.php
```
<?php
    if (! function_exists('get_extension')) {
        function get_extension($file)
        {
            return pathinfo($file, PATHINFO_EXTENSION);
        }
    }
```

修改composer.json文件
```
"autoload": {
    "classmap": [
        "database"
    ],
    "psr-4": {
        "App\\": "app/"
    },
    "files": [
        "app/Common/Helpers.php"
    ]
}
```

在autoload配置项添加了一个 "files"，把创建的辅助函数文件路径添加进去

在命令行中运行 `composer dumpauto` 使修改生效

自动加载原理：
[https://my.oschina.net/bluebellx7/blog/1545327](https://my.oschina.net/bluebellx7/blog/1545327)

Composer自动载入：[https://www.toolmao.com/composer-autoload](https://www.toolmao.com/composer-autoload)

原文链接：[https://blog.csdn.net/bluebellx7/article/details/84670450](https://blog.csdn.net/bluebellx7/article/details/84670450)