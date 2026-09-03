---
layout: post
title: Dcat Admin 列表添加子列表
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

#### Dcat Admin 列表添加子列表
1. 控制器添加字段
```
$grid->column('hotelordercount', '订单数量')->display(function () {
    return \App\Models\ProductHotelOrder::query()->where('settle_id', $this->id)->count();
})->expand(HotelOrderStatistics::make());
```
2. 添加Renderable渲染层
	```HotelOrderStatistics.php
	<?php
	namespace App\Admin\Renderable;
	use App\Models\ProductHotelOrder;
	use App\Models\ProductHotelOrderInfo;
	use Dcat\Admin\Support\LazyRenderable;
	use Dcat\Admin\Widgets\Table;

	class HotelOrderStatistics extends LazyRenderable
	{

			public function render()
			{
					$data = [];
					$res = ProductHotelOrder::query()->where('settle_id',$this->key)->orderBy('id', 'desc')->get()->toArray();
					foreach ($res as $k=>$v) {
							$product_names=ProductHotelOrderInfo::query()->where('order_id',$v['id'])->pluck('name')->toArray();
							$product_names=implode(',',$product_names);
							$total_price_arr=json_decode($v['price'],true);
							$total_price=$total_price_arr['total_price'];
							$status = match ($v['status']) {
									-1 => '订单截停',
									0=>'取消订单',
									1=>'待确认',
									2=>'待发货',
									3=>'发货中',
									4=>'待收货确认',
									5=>'已结束',
									default=>'售后',
							};
							$data[] = [
									$v['id'],
									$v['order_number'],
									$product_names,
									$total_price,
									$status,
									$v['created_at'],
							];
					}

					return Table::make(['订单Id', '订单号', '订单商品', '订单价格','订单状态','下单时间'], $data);
			}
	}
	```