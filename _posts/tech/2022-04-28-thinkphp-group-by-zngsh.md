---
layout: post
title: ThinkPHP group by自定义规则分组
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

原生语句

```
SELECT 
CASE WHEN `type` IN('A','C','D') THEN 'ACD'
WHEN `type` IN ('F', 'G') THEN 'FG'
ELSE `type` END AS `types`, SUM(num)
FROM demo
GROUP BY `types` 

等同于：
SELECT 
CASE WHEN `type` IN('A','C','D') THEN 'ACD'
WHEN `type` IN ('F', 'G') THEN 'FG'
ELSE `type` END AS `types`, SUM(num)
FROM demo
GROUP BY 
CASE WHEN `type` IN('A','C','D') THEN 'ACD'
WHEN `type` IN ('F', 'G') THEN 'FG'
ELSE `type` END

```

ThinkPHP语句

```
$list = Db::name('Demo')
    ->field("SUM(num),CASE WHEN `type` IN('A','C','D') THEN 'ACD'
        WHEN `type` IN ('F', 'G') THEN 'FG'
        ELSE `type` END AS `types`")
    ->order('id DESC')->select();
```

参考网址：

[https://www.jianshu.com/p/c9a66823ef64?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation](https://www.jianshu.com/p/c9a66823ef64?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation)

[https://blog.csdn.net/qq_24504453/article/details/78807104](https://blog.csdn.net/qq_24504453/article/details/78807104)