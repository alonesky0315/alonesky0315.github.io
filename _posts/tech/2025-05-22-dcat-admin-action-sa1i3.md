---
layout: post
title: Dcat Admin 添加action按钮
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

#### Dcat Admin 列表添加子列表
1. 控制器添加Actions字段
```
$grid->actions(function (Grid\Displayers\Actions $actions) {
    $actions->append(new HotelBillStatus());
});
```
2. 添加Action操作
```HotelBillStatus.php
    <?php

    namespace App\Admin\Action\HotelOrder;

    use App\Models\HotelBill;
    use App\Models\ProductHotelOrder;
    use Dcat\Admin\Actions\Response;
    use Dcat\Admin\Grid\RowAction;
    use Illuminate\Http\Request;

    class HotelBillStatus extends RowAction
    {

        protected $model;

        public function __construct(string $model = null)
        {
            $this->model = $model;
        }

        /**
         * @return string
         */
        protected $title = '设为已支付';

        /**
         * Handle the action request.
         *
         * @param Request $request
         *
         * @return Response
         */
        public function handle()
        {
            // dump($this->getKey());

            // 获取当前行ID
            $id = $this->getKey();
            // 获取 parameters 方法传递的参数
            $objVerify = HotelBill::query()->find($id);
            if (empty($objVerify)) {
                return $this->response()->error('数据不存在')->refresh();
            }
            $objVerify->status = 2;
            if ($objVerify->save()){
                ProductHotelOrder::query()->where('settle_id',$id)->update(['status'=>5]);
                return $this->response()
                    ->success('操作成功')
                    ->refresh();
            }
            return $this->response()
                ->success('操作失败')
                ->refresh();
        }

        /**
         * @return string|array|void
         */
        public function confirm()
        {
            return ['确定设置账单为已支付吗?', ''];
        }

        /**
         * @return array
         */
        protected function parameters()
        {
            return [
                /*   // 发送当前行 主键ID
                'ids'     => $this->row->id,
                'operate' => 1,*/
            ];
        }
    }
```