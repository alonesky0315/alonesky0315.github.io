---
layout: post
title: ThinkPHP5验证器中唯一性验证unique的问题
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

如果是在添加界面,验证规则一般这样

```
protected $rule=[
    'title|标题' => 'require|length:2,10|unique:place',
    // 表示验证name字段的值是否在user表（不包含前缀）中唯一
    'name'   => 'unique:user',
    // 验证其他字段
    'name'   => 'unique:user,account',
    // 排除某个主键值
    'name'   => 'unique:user,account,10',
    // 指定某个主键值排除
    'name'   => 'unique:user,account,10,user_id',
    // 多个字段验证唯一验证条件
    'name'   => 'unique:user,status^account',
    // 复杂验证条件
    'name'   => 'unique:user,status=1&account='.$data['account'],
];
```

查询sql是这样的
```
SELECT `id` FROM `place` WHERE `title` = '临沂' LIMIT 1
```

像这样的规则,添加是正常的,在编辑界面验证的时候,提交自身的数据常常会提示重复

在表单中把主键id也作为数据传入到验证器中
```
<input type="hidden" value="{$id}" name="id" />
```
验证规则没有改动,在验证的时候,验证用的sql语句自动变了

```
SELECT `id` FROM `place` WHERE `title` = '临沂' AND `id` <> 1 LIMIT 1
```

在验证数据中传入主键值,修改时在验证唯一性的时候,会排除传入主键的id的数据。

原文链接：[https://www.cnblogs.com/PHPaki/p/8438962.html](https://www.cnblogs.com/PHPaki/p/8438962.html)

ThinkPHP内置验证：[https://www.kancloud.cn/manual/thinkphp5/129356](https://www.kancloud.cn/manual/thinkphp5/129356)