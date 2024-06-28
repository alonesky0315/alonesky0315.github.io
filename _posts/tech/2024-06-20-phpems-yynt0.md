---
layout: post
title: phpems考试系统使用指南
category: Common
keywords: Common PHP
tags: Common PHP
description: 
---

> phpems考试系统使用指南

#### 添加用户时开通课程、考场

1. 用户模型添加要使用的模型
```php
// app\user\app.php
// 考场类
$this->basic = \PHPEMS\ginkgo::make('basic','exam');
// 课程类
$this->course = \PHPEMS\ginkgo::make('course','course');
```
2. 控制器
```php
// app\user\controller\user.master.php
// 开通考试
$this->basic->openBasic(array('obuserid'=>'用户Id','obbasicid'=>'考场Id','obendtime' => TIME + 30*24*3600));
// 开通课程
$this->course->openCourse(array('ocuserid' => '用户Id', 'occourseid' => '课程Id', 'ocendtime' => TIME + 30 * 24 * 3600));
```