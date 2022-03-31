---
layout: post
title: Laravel常识
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

1.  打印SQL语句
```
DB::enableQueryLog();
$version_list = (array)Db::table('version')->pluck('version')->toArray();
dd(DB::getQueryLog());
```
参考链接：[https://learnku.com/laravel/wikis/27707](https://learnku.com/laravel/wikis/27707)