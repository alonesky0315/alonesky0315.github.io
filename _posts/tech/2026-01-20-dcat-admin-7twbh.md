---
layout: post
title: Dcat Admin知识库四(数据表单)
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

四、 数据表单
1. 表头
2. 字段
	```
	// 不显示字段
	$form->display('id');

	// 隐藏字段并设置默认值
	$form->hidden('type')->value(1);

	// 下拉列表
	$form->select('cid')->options(Categories::where('type', 1)->pluck('name', 'id'))->required();
	
 // 多选下拉列表
 $form->multipleSelect('aid')
	->options(Articles::where('type', 1)
		->pluck('title', 'id'))
	->saving(function ($value) {
		return $value ? implode(',', $value) : '';
	})
	->required();

	// 最大长度
	$form->text('title')->required()->maxLength(50);

	// 文本域及可填写行数
	$form->textarea('describe')->rows(4)->required();

	// 百度编辑器(单独安装)
	// https://packagist.org/packages/weiaibaicai/ueditor
	$form->ueditor('content')->required();

	// Dcat-admin默认编辑器(TinyMCE)
	$form->editor($column[, $label]);

	// 设置语言包
	$form->editor('content')->languageUrl(url('TinyMCE/langs/de.js'));

	// 标签(需要输入逗号回车)
	$form->tags('tags')->saving(function ($value) {
		return $value ? implode(',', $value) : '';
	});

	// 添加默认日期
	$form->datetime('published_at')->default(now());
	
	// 日期范围
	$form->dateRange('date_start', 'date_end', '日期范围')
	->options([
		'locale'  => ['format' => 'YYYY-MM-DD'],
		'minDate' => date('Y-m-d 00:00:00', strtotime('+1 day')), // 最小明天
		// 加上23:59:59是为了解决最后一天选不上的问题
		'maxDate' => date('Y-m-d 23:59:59', strtotime('+1 month')) // 最大1个月后
	])
	->default([
		'date_start'=>date('Y-m-d', strtotime('+1 day')),
		'date_end'=>date('Y-m-d', strtotime('+7 day')),
	])
	->required();

	// 数字输入框，并赋值
	$form->number('order')->default(0);

	// 单选框
	$form->radio('status')->options([1 => '正常', 2 => '禁用'])->default(1);

	// 单图上传
	// saveFullUrl() 保存远程路径
	$form->image('image')->saveFullUrl()->autoUpload();
	
	// 多图上传
	$form->multipleImage('image')->saveFullUrl()->autoUpload();
	
	// 密码为空不修改
	$form->password('password')
	->rules("required|regex:/(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?!.*\s).{8,20}/", [
		'required' => '密码必填',
		'regex' => '密码必须为8~20位的大写+小写+数字三种格式组成',
	])
	->saving(function ($value) use ($form) {
		// 获取原始模型（新建时为null，编辑时为数据库中的模型）
		$originalModel = $form->model();
		return $value ? bcrypt(trim($value)) : ($originalModel ? $originalModel->password : '');
	})
	->help('不填写则保持不变');
	
	// 下拉选框联动 (load)
	$form->select('warehouse_id')
		->options(Warehouse::query()->pluck('name', 'id')->toArray())
		->load('warehouse_user_id', 'getFilterList')
		->required();
	$form->select('warehouse_user_id')->required();

	// 下拉选框多联动 (loads)
	$form->select('warehouse_id')
		->options(Warehouse::query()->pluck('name', 'id')->toArray())
		->loads(['warehouse_user_id','user_id'], ['getFilterList'),'getUserList')]
		->required();
	$form->select('warehouse_user_id')->required();
	$form->select('user_id')->required();

	// 字段动态显示
	$form->radio('type')->options([1 => '个人', 2 => '企业'])
    ->when(1, function (Form $form) {
        $form->image('images')->autoUpload();
    })
    ->when(2, function (Form $form) {
        $form->text('company');
        $form->image('logo')->autoUpload();
    })->default(1)->required();
	
	// 提交时忽略字段，提交后取值并过滤移除的
	$form->saving(function (Form $form) {
		$form->deleteInput('freezer_info');
	});
	$freezerInfo=$form->freezer_info;
	$form->saved(function (Form $form)use($freezerInfo) {
		$filteredFreezerInfo = array_filter($freezerInfo, function ($item) {
			// 保留 _remove_ 字段不存在、为空或不为 '1' 的行
			return empty($item['_remove_']) || $item['_remove_'] !== '1';
		});
	});
	
	// 树形
	$form->tree('role_ids')
	->nodes(function () {
		$WhRoleMenuModel = new WhRoleMenu();
		$whRoleMenuList = $WhRoleMenuModel->where('type', 2)->select('id', 'pid', 'name')->get();
		return $whRoleMenuList;
	})
	->setParentColumn('pid');
	// dcat系统写法
	// ->customFormat(function ($v) {
	//  if (! $v) {
	//      return [];
	//  }
	//  return array_column($v, 'id');
	// });
	
	// 下拉动态关联表格
	$form->select('warehouse_id')
		->options(Warehouse::query()->pluck('name', 'id')->toArray())
		->attribute('id', 'warehouse_id')
		->required();
	$form->table('products', function ($table) {
		$table->hidden('product_id'); // 名称只读，避免修改
		$table->text('name'); // 名称只读，避免修改
		$table->select('unit')->options([
			'个' => '个',
			'件' => '件',
			'箱' => '箱',
			'米' => '米',
			'千克' => '千克',
		])->attribute('style', 'width: 200px;')->required(); // 单位必填
		$table->number('stock')->attribute('style', 'width: 10px !important;')->default(0); // 库存从数据库获取，只读
		$table->number('inventory')->attribute('style', 'width: 10px !important;')->default(0)->required(); // 盘点数量手动填写
	})->default();
	
	$form->html(<<<HTML
	<style>
			.has-many-table-products .fields-group td:nth-child(6),
			.has-many-table-products .add {
					display: none;
			}
	</style>
	HTML);
	if ($form->isCreating()) {
		// 3. 极简JS：监听load.success事件，复用返回值
		$form->html(<<<HTML
		<script>
		$(function(){
			// 定义Table赋值核心函数（极简版）
			function setTableData(data) {
				// 清空原有行（排除模板行）
				$('.has-many-table-products .fields-group').remove();
				// 遍历数据新增行并赋值
				$.each(data, function(index, row) {
					console.log(row);
					// 触发新增行按钮
					$('.has-many-table-products .add').click();
					// 延迟赋值（确保DOM加载）
					setTimeout(function() {
						var current = $('.has-many-table-products .fields-group').eq(index);
						current.find('[name*="[product_id]"]').val(row.id);
						current.find('[name*="[name]"]').val(row.name);
						var unitSelect = current.find('[name*="[unit]"]');
						unitSelect.val(row.unit || ''); // 赋值value（如"个"/"件"）
						unitSelect.trigger('change');
						current.find('[name*="[stock]"]').val(row.stock);
						current.find('[name*="[inventory]"]').val(row.inventory);
					}, 100);
				});
			}
			// 监听warehouse_id的load.success事件
			$('#warehouse_id').on('change', function(e, res) {
				var warehouseId = $(this).val();
				if (!warehouseId) {
					return;
				}
				// 手动请求原 getFilterList 接口
				$.ajax({
					url: '/admin/getWarehouseProductList', // 接口地址
					type: 'GET', // 必须和后端请求方式一致（默认GET）
					data: {warehouseId: warehouseId}, // 参数（键名和后端保持一致）
					dataType: 'json', // 强制指定返回格式为JSON（2.2.0-beta 必加）
					success: function(res) {
						// 后续赋值逻辑（不变）
						if (res.data && res.data.length) {
							setTableData(res.data);
						}
					},
					error: function(xhr) {
						console.error('请求失败：', xhr.code, xhr.msg); // 排查错误
					}
				});
			});
		});
		</script>
		HTML);
	}

	// Controller
	public function getWarehouseProductList(Request $request)
	{
		$warehouseId = $request->input('warehouseId');
		if (!$warehouseId) {
			return response()->json(['code' => 1, 'msg' => '请选择仓库', 'data' => []]);
		}
		// 获取仓库关联的产品 ID
		$productIds = WarehouseModel::query()
			->where('id', $warehouseId)
			->value('product');
		$productIds = json_decode($productIds, true) ?? [];
		if (empty($productIds)) {
			return response()->json(['code' => 0, 'msg' => '暂无产品', 'data' => []]);
		}
		// 获取产品基础信息
		$productList = Product::query()
			->whereIn('id', $productIds)
			->select(['id', 'name', 'stock'])
			->get()
			->toArray();
		// 获取对应仓库的库存
		$stockArr = ProductAttrValue::query()
			->whereIn('product_id', $productIds)
			->where('warehouse_id', $warehouseId)
			->where('hotel_id', 0)
			->pluck('stock', 'product_id')
			->toArray();
		// 组装返回数据
		foreach ($productList as &$item) {
			$item['product_no'] = 2000 + $item['id']; // 产品编号
			$item['stock'] = $stockArr[$item['id']] ?? 0; // 实际库存
			$item['unit'] = ''; // 单位默认空，前端选择
			$item['inventory'] = ''; // 盘点数量默认空，前端填写
		}
		unset($item); // 解除引用
		return response()->json([
			'code' => 0,
			'msg' => 'success',
			'data' => $productList
		]);
	}
	// Model
	public function getProductsAttribute($products)
	{
		return array_values(json_decode($products, true) ?: []);
	}
	public function setProductsAttribute($products)
	{
		$this->attributes['products'] = json_encode(array_values($products));
	}
```