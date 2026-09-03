---
layout: post
title: Dcat Admin知识库二(数据表格)
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

二、 数据表格
1. 排序
	```
	// 排序
	$grid->model()->orderBy('created_at', 'desc');
	
	//  FIELD排序
	// 指定FIELD排序
	$grid->model()->orderByRaw(DB::raw('FIELD(use_in, 1,0,2) asc'));

	// 指定顺序排序
	DB::name('user')->orderByRaw(DB::raw('FIELD(use_in, 1,0,2) asc'));
	```

2. 过滤   
	```
	// 快速筛选样式 
	$filter->panel();

	// 过滤设置宽度
	$filter->equal('id')->width(1);

	// 过滤添加下拉
	$filter->equal('hotel_id', '所属酒店')->select(Hotel::query()->pluck('name', 'id')->toArray())->width(3);

	// 过滤添加下拉使用(find_in_set)
	$ArticlesList = Articles::where('type', 1)->pluck('title', 'id')->toArray();
	$filter->findInSet('aid')->select($ArticlesList);

	// 模糊匹配
	$filter->like('name')->width(2);

	// 省市区联动
	$filter->distpicker('hotel.province_id', 'hotel.city_id', 'hotel.district_id', '地域选择')->width(4);

	// 过滤添加快捷筛选
	$filter->scope('today', '今日')->whereDate('pay_date', today());
	$filter->scope('yesterday', '昨日')->whereDate('pay_date', today()->subDay());
	$filter->scope('this_week', '本周')->whereBetween('pay_date', [Carbon::now()->startOfWeek(), Carbon::now()->endOfWeek()]);
	$filter->between('pay_date', '日期')->date()->width(3);

	// 过滤添加单选框
	$filter->equal('status')->radio([1 => '正常', 2 => '禁用']);
	
	// 指定角色的管理员
	$adminList = Administrator::query()
	->whereHas('roles', function ($query) {
		// whereHas 筛选「关联了 role_id=3 角色」的管理员
		$query->where('role_id', 3);
	})
	->pluck('name', 'id')
	->toArray();
	$filter->equal('admin_id')->select($adminList)->width(2);

	// select筛选
	$filter
	->where('warehouse_id', function ($query) {
		/** @var ProductModel $this */
		if ($this->input === '') {
			// 选全部，不做筛选
			return; 
		} else {
			// 是：查warehouse_id≠0的所有值
			$query->where('warehouse_id', '!=', 0); 
		}
	}, '是否平台产品')
	->select([
		// 可选：添加全部选项，提升用户体验
		'' => '全部', 
		1 => '是'
	])
	->width(2);
	```
3. 表头
	```
	// 左右滚动条
	$grid->scrollbarX();

	// 每页默认条数
	$grid->paginate(10);

	// 禁用创建按钮
	$grid->disableCreateButton();
	
	// 启用弹窗创建
    $grid->enableDialogCreate();

	// 禁用编辑按钮
	$grid->disableEditButton();
	
	// 显示快速编辑按钮
    $grid->showQuickEditButton();

	// 禁用删除按钮
	$grid->disableDeleteButton();

	// 禁用详情按钮
	$grid->disableViewButton();

	// 禁用批量操作选项
	$grid->disableBatchActions();

	// 禁用快速删除选项
	$grid->disableQuickDeleteButton();

	// 禁用快速删除选项
	$grid->disableQuickEditButton();

	// 禁用行选择器
	$grid->disableRowSelector();

	// 禁用列选择器
	$grid->disableColumnSelector();

	// 指定表格操作列样式
	$grid->setActionClass(Grid\Displayers\Actions::class);

	// 开启字段选择器功能
	$grid->showColumnSelector();

	// 设置默认隐藏字段
	$grid->hideColumns(['field1', ...]);

	// 过滤条件
	$grid->model()->where('type', 1);

	// 默认分页数
	$grid->paginate(perPage: 10);

	// 固定列
	// 第二个参数可不传，默认为 - 1
	$grid->fixColumns(固定从头开始的列数, 固定从后往前数的列数);

	// 自定义工具栏添加按钮
	$grid->tools(function (Grid\Tools $tools) {
		$tools->append(Modal::make()
		// 大号弹窗
		->lg()
		// 弹窗标题
		->title('追加订单')
		// 按钮
		->button('<button class="btn btn-primary"><i class="feather icon-plus"></i> 追加订单</button>')
		// 弹窗内容
		->body(HotelOrderAdd::make()));
	});
	
	// 动态操作按钮
	$grid->actions(function (Grid\Displayers\Actions $actions) {
		$actions->append(new IncreaseStock());
			if (!Admin::user()->isAdministrator()) {
				/** @var ProductModel $this */
				if(empty($this->warehouse_id)){
					$actions->disableDelete();
					$actions->disableEdit();
				}
		}
});

	// 关联表
	return Grid::make(Articles::with('categories'), function (Grid $grid) {
		……
	});

	// 导出
	$grid->export()->filename('销售额统计');
	```
4. 字段   
```
    // 可排序
    $grid->column('id')->sortable();

    // 缩略图尺寸
    $grid->column('image')->image('', 50, 50);

    // 字数限制
    $grid->column('describe')->limit(100);

    // 标签话
    $grid->column('tags')->label();

    // 可编辑
    $grid->column('views')->sortable()->editable();

    // 可编辑单选框
    $grid->column('status')->radio([1 => '正常', 2 => '禁用'])->sortable();
    
    // 可编辑复选框
    $grid->column('status')->checkbox([1 => '正常', 2 => '禁用'])->sortable();
    
    // 可编辑下拉
    $grid->column('status')->select([1 => '正常', 2 => '禁用'])->sortable();
    
    // 可编辑开关
    $grid->column('status')->switch()->sortable();

    // 字段渲染
    $grid->column('type')->using([1 => '新闻', 2 => '快讯', 3 => '专题']);

    // 字段动态显示
    $grid->column('aid')->display(function ($value) {
        /** @var \App\Models\Read $this */
        if ($this->type != 3) {// 文章或快讯
            return \App\Models\Articles::where('id', $value)->value('title');
        }else{// 专题
            return \App\Models\Topic::where('id', $value)->value('title');
        }
    });
    
    // 复制字段内容
    $grid->column('图片路径')->display(function(){
        return <<<HTML
            <div class="input-group">
                <input type="text" id="image-path-{$this->getKey()}" class="form-control " value="{$this->image_urls}" readonly>
                <button class="btn btn-success" type="button" onclick="copyImagePath('{$this->getKey()}')">
                    <i class="feather icon-copy"></i>
                </button>
            </div>
            <!-- 复制功能的 JS 脚本 -->
            <script>
            function copyImagePath(rowKey) {
                // 获取输入框元素
                const input = document.getElementById('image-path-' + rowKey);
                // 选中内容并复制
                input.select();
                document.execCommand('copy');

                // 提示复制成功（使用 Dcat Admin 内置的消息提示）
                Dcat.success('图片路径已复制到剪贴板！');
            }
            </script>
        HTML;
        })
        ->help('点击按钮可复制当前行的图片路径')
        ->width(200);
    
    // 列表异步加载弹窗数据表格简洁
    use Dcat\Admin\Widgets\Table;
    $grid->column('aid')
        ->display('查看') // 设置按钮名称
        ->modal(function ($modal) {
            $modal->title('新闻列表');
            // 解决$this报错问题
            /** @var \App\Models\Topic $this */
            $freezerList = [];
            if (!empty($this->freezer_info)) {
                $freezerInfo = json_decode($this->freezer_info, true);
                foreach ($freezerInfo as $key => $item) {
                    $freezerList[] = [
                        ($key+1),
                        $item['code'],
                    ];
                }
            }
            return Table::make(['序号','编号'], $freezerList);
        });

    // 列表异步加载弹窗数据表格
    $grid->column('aid')
        ->display('查看') // 设置按钮名称
        ->modal(function ($modal) {
            $modal->title('新闻列表');
            // 允许在闭包内返回异步加载类的实例
            return App\Admin\Renderable\Topic::make(['type' => 1, 'aid' => $this->aid]);
        });
        
        // 列表异步加载下拉数据表格简洁
        $grid->column('product_snapshot')->display('套餐商品')
        ->expand(function () {
            /** @var PackageOrderModel $this */
            $titles = [
                '序号',
                '商品ID',
                '商品名称',
                '商品图片',
                '商品单价',
                '商品数量',
            ];
            $snapshotRaw = json_decode($this->product_snapshot, true) ?? [];
            $snapshot = [];

            foreach (array_values($snapshotRaw) as $index => $item) {
                $snapshot[] = [
                    $index + 1, // 序号从 1 开始
                    $item['product_id'] ?? '-',
                    $item['name'] ?? '-',
                    $item['photo'] ? '<img data-action="preview-img" src="'.$item['photo'].'" style="max-width:100px;max-height:100px;cursor:pointer" class="img img-thumbnail">' : '-',
                    $item['unit_price'] ?? '-',
                    $item['quantity'] ?? '-',
                ];
            }
            return Table::make($titles, $snapshot) ?? '-';
        });
				// 列表异步加载下拉数据表格简洁
        $grid->column('product_snapshot')->display('套餐商品')
        ->expand(function () {
					return App\Admin\Renderable\Topic::make(['type' => 1, 'aid' => $this->aid]);
				});

    // App\Admin\Renderable\Topic.php
    <?php
    namespace App\Admin\Renderable;

    use App\Models\Articles;
    use Dcat\Admin\Support\LazyRenderable;
    use Dcat\Admin\Widgets\Table;

    class Topic extends LazyRenderable
    {
        public function render()
        {
            // 获取其他自定义参数
            $type = $this->type;
            $aid = $this->aid;
            // 方式一
            $data = Articles::whereIn('id', explode(',', $aid))
                ->where('type', $type)
                ->select('id', 'title', 'published_at', 'created_at')
                ->get()->toArray();
            // 方式二
            $data = [];
            $res = Articles::whereIn('id', explode(',', $aid))
                ->where('type', $type)
                ->get()->toArray();
            foreach ($res as $k=>$v) {
                $data[] = [
                    $v['id'],
                    $v['title'],
                    $v['published_at'],
                    $v['created_at'],
                ];
            }
            $titles = [ 'ID', '标题', '发布时间', '创建时间'];
            return Table::make($titles, $data);
        }
    }
    
    // 剩余时间
    $grid->column('last_time', '剩余时间')->display(function ($value) {
        // 1. 获取 end_time 的 Carbon 对象（Laravel 模型自动转换，无需手动处理）
        /** @var MushroomModel $this */
        $endTime = $this->end_time;

        // 2. 空值处理：无结束时间返回占位符
        if (empty($endTime)) {
                return '-';
        }

        // 3. 确保是 Carbon 对象（兼容手动赋值的字符串时间）
        if (!($endTime instanceof Carbon)) {
                $endTime = Carbon::parse($endTime);
        }

        // 4. 判断是否已过期（Carbon 语义化方法）
        if ($endTime->isPast()) {
                return '<span style="color: #ff4d4f;">已过期</span>';
        }

        // 5. 计算剩余时间（Carbon 便捷方法）
        $now = Carbon::now(); // 当前时间（自动适配时区）
        $diffDays = $endTime->diffInDays($now); // 剩余天数
        $diffHours = $endTime->diffInHours($now) % 24; // 剩余小时（扣除天数后）
        $diffMinutes = $endTime->diffInMinutes($now) % 60; // 剩余分钟（扣除小时后）
        // $diffSeconds = $endTime->diffInSeconds($now) % 60; // 剩余秒数（可选）

        // 6. 拼接人性化剩余时间（只显示非零部分）
        $remainingTime = '';
        if ($diffDays > 0) {
                $remainingTime .= $diffDays . '天';
        }
        // if ($diffHours > 0) {
        //     $remainingTime .= $diffHours . '时';
        // }
        // if ($diffMinutes > 0) {
        //     $remainingTime .= $diffMinutes . '分';
        // }
        // 可选：显示秒数
        // if ($diffSeconds > 0) {
        //     $remainingTime .= $diffSeconds . '秒';
        // }

        // 7. 绿色标注剩余时间
        return '<span style="color: #52c41a;">剩余' . $remainingTime . '</span>';
    });
```