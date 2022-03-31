---
layout: post
title: PHP防止订单超卖,秒杀,限购,PHP高并发防止超卖代码实践
category: PHP
keywords: 
tags: 
description: 
---

建表

1. 订单表

```
CREATE TABLE `order` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `order_sn` varchar(45) NOT NULL DEFAULT '0' COMMENT '订单编号',
  `goods_id` int(11) NOT NULL DEFAULT '0' COMMENT '商品id',
  `uid` int(11) NOT NULL DEFAULT '0' COMMENT '用户id',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8 COMMENT='订单表';
```

2. 库存表

```
CREATE TABLE `stock` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `goods_id` int(11) NOT NULL DEFAULT '0' COMMENT '商品id',
  `num` int(11) NOT NULL DEFAULT '0' COMMENT '剩余库存',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8 COMMENT='库存';
```

3. 在库存表插入一条数据id=1, goods_id=1,num=10

```
INSERT INTO `stock` (`id`,`goods_id`,`num`) VALUES (1,1,10)
```

测试超卖

> 使用PHP的laravel框架测试

PHP代码

```
public function create_order()
{
    //模拟接受到的数据
    $params = [
        'goods_id' => 1,//商品id
        'uid' => mt_rand(1, 1000),//随机用户uid
        'num' => 1,//购买的数量
    ];
    //查库存
    $stockInfo = DB::table('stock')->where('goods_id', $params['goods_id'])->first('num');
    $remain = $stockInfo->num - $params['num'];
    if ($remain >= 0) {
        $data = [
            'order_sn' => date('YmdHis') . mt_rand(1000, 9999),
            'goods_id' => $params['goods_id'],
            'uid' => $params['uid'],
        ];
        //插入订单
        DB::table('order')->insert($data);
        //更新库存--num字段是无符号类型
        DB::table('stock')->where('goods_id', $params['goods_id'])->decrement('num', $params['num']);
        return '抢到了';
    } else {
        return '暂无库存';
    }
}

// 使用ab压测(ab压测详细使用https://www.cnblogs.com/wangzhaobo/p/8296298.html)
ab -c 100 -n 100 "https://file.alonesky.com/create_order"

```

> 如果没有出现超卖，就再测试一下，一般3次之内就会出现超卖

> 防止超卖方法

1.使用mysql排它锁

```
//使用mysql排它锁防止超卖
public function create_order_lock()
{
    //模拟接受到的数据
    $params = [
        'goods_id' => 1,//商品id
        'uid' => mt_rand(1,1000),//随机用户uid
        'num' => 1,//购买的数量
    ];
    //开始事物
    DB::beginTransaction();
    //查库存
    $stockInfo = DB::table('stock')->where('goods_id',$params['goods_id'])->lockForUpdate()->first('num');
    $remain = $stockInfo->num-$params['num'];
    if($remain>=0){
        $data = [
            'order_sn' => date('YmdHis').mt_rand(1000,9999),
            'goods_id' => $params['goods_id'],
            'uid' => $params['uid'],
        ];
        //插入订单
        DB::table('order')->insert($data);
        //更新库存
        DB::table('stock')->where('goods_id',$params['goods_id'])->decrement('num',$params['num']);
        //提交事务
        DB::commit();
        return '抢到了';
    }else{
        //回滚事务
        DB::rollBack();
        return '暂无库存';
    }
}

// 使用ab测试
ab -c 100 -n 100 "https://file.alonesky.com/create_order_lock"

```

2.使用redis队列

> 先访问设置库存,再下单

```
//设置库存
public function set_stock()
{
    //模拟接受到的数据
    $params = [
        'goods_id' => 1,//商品id
        'uid' => mt_rand(1,1000),//随机用户uid
        'num' => 1,//购买的数量
    ];
    $rdKey = 'goods_id_'.$params['goods_id'];
    $num = DB::table('stock')->where('goods_id',$params['goods_id'])->first('num')->num;
    for($i=0;$i<$num;$i++){
        Redis::lpush($rdKey,1);
    }
    return '设置库存成功'.$num;
}

//使用redis防止超卖
public function create_order_redis()
{
    //模拟接受到的数据
    $params = [
        'goods_id' => 1,//商品id
        'uid' => mt_rand(1,1000),//随机用户uid
        'num' => 1,//购买的数量
    ];
    $rdKey = 'goods_id_'.$params['goods_id'];

    $num = Redis::lpop($rdKey);
    if($num!=1){
        return '没货了';
    }else{
        $data = [
            'order_sn' => date('YmdHis').mt_rand(1000,9999),
            'goods_id' => $params['goods_id'],
            'uid' => $params['uid'],
        ];
        //插入订单
        DB::table('order')->insert($data);
        return '抢到了';
    }
}

// 先访问设置库存
ab -c 1 -n 1 "https://file.alonesky.com/set_stock"
// 后使用ab测试多次并发下单
ab -c 100 -n 100 "https://file.alonesky.com/create_order_redis"

```
3.使用异步队列

> 把下单扔到队列里面,前端轮询,用户排队等待结果

原文链接：

PHP防止订单超卖：[https://www.cnblogs.com/wangzhaobo/p/14415164.html](https://www.cnblogs.com/wangzhaobo/p/14415164.html)

Apache ab压测详细使用：[https://www.cnblogs.com/wangzhaobo/p/8296298.html](https://www.cnblogs.com/wangzhaobo/p/8296298.html)