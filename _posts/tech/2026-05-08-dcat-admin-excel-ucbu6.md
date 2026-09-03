---
layout: post
title: Dcat Admin 导出Excel
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

####  Dcat Admin 导出Excel

```php
// HotelOrderController
protected function grid() {
    return Grid::make(new HotelOrder([ 'warehouse']), function (Grid $grid) {
        
        ...

        $header = [
            'id' => 'ID',
            'order_number' => '订单号',
            'warehouse_name' => '方舱',
            'hotel_name' => '所属酒店',
            'status' => '状态',
        ];
        $grid->export($header)->rows(function ($rows) {
            // 状态：0=取消订单，1=待确认
            foreach ($rows as $index => &$row) {
                switch ($row['status']) {
                    case 0:
                        $row['status'] = '已取消';
                        break;
                    case 1:
                        $row['status'] = '待确认';
                        break;
                }
            }
            return $rows;
        });
    });
}
```