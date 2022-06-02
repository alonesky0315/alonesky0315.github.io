---
layout: post
title: ThinkPHP连表求和
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

```
$list = $this
	->alias('us')
	->field("us.id,ifnull(up.sales,0) as sales,us.name,us.mobile,us.lat,us.lng,us.distance,us.address_map,
	(6371 * acos (cos ( radians($lat) ) * cos( radians( lat ) ) * cos( radians( lng ) - radians($lng) ) + 
	sin ( radians($lat) ) * sin( radians( lat ) ))
	) AS far")
	->join('(select shop_id, sum(sales+real_sales) as sales from product group by shop_id) up','us.id=up.shop_id','left')
	->select();
```
原文链接：[https://zhidao.baidu.com/question/693773367807678244.html](https://zhidao.baidu.com/question/693773367807678244.html)