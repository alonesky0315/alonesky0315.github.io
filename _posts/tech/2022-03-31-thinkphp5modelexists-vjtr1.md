---
layout: post
title: thinkphp5使用model查询数据时exists作为条件的用法
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

在thinkphp框架中如果使用model查询数据，有时候需要用到exists作为条件，具体代码应该怎么写呢？下面我们就来看看。

假设我们的需求是要查询今天有登陆过系统的用户，直接写sql的话应该是:
```
select * from user where exists ( select id from user where user_id=id );
```

那么，转变成用thinkphp框架就应该写成这样：

```
<?php
$where = [
    ['', 'exists', Db::raw('select id from user where user_id='.$id)]
];
$result = UserModel::where($where)->select();
?>
```

原文链接：[https://www.netljc.com/article/detail-81](https://www.netljc.com/article/detail-81)