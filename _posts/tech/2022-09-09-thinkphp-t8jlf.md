---
layout: post
title: ThinkPHP多态
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

```
$list = $this->model
            ->with(['ordergoods'])
            ->where($where)
            ->order($sort, $order)
            ->limit($offset, $limit)
            ->select();
public function ordergoods()
{
    return $this->morphTo(['order_type','order_goods_id'],[
        'groups'=>  'app\api\model\groups\OrderGoods',
        'goods' =>  'app\api\model\OrderGoods',
    ]);
}
```
ThinkPHP多态手册：[https://www.kancloud.cn/manual/thinkphp5/250866](https://www.kancloud.cn/manual/thinkphp5/250866)