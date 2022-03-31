---
layout: post
title: ThinkPHP5 无法获取session的原因和对策
category: PHP
keywords: 
tags: PHP 小程序 常识
description: 
---

### Thinkphp5 的sesssion在同一个控制器不同的方法无法获取session的原因和对策
  这一段在用thinkPHP5开发微信小程序接口的时候，在同一个控制器一个方法中存入session，在另一个方法中取出session，无法取出。   
  网上找到原因：thinkPHP5里面的session是给浏览器用的，非浏览器的方式是没有办法访问到那个session的，只能用其他方式来代替session。    
（推荐）使用TP5自带的缓存方法   
```
Cache::set('name',$value,3600);
```
//获取缓存数据可以使用：     
```
Cache::get('name');
```   
原文链接：[https://www.cnblogs.com/piaobodewu/p/9321269.html](https://www.cnblogs.com/piaobodewu/p/9321269.html)