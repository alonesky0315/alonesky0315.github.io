---
layout: post
title: Dcat Admin知识库三(数据详情)
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

三、 数据详情
1. 表头
	```
	// 禁用编辑按钮
	$show->disableEditButton();

	// 禁用删除按钮
	$show->disableDeleteButton();
	
	// 动态操作按钮
	if(empty($show->model()->warehouse_id)){
		$show->disableDeleteButton();
		$show->disableEditButton();
	}
	```
2. 字段
	```
	// html不转义
	$show->field('content')->unescape();

	// 文本域样式
	$show->field('describe')->unescape()->as(function ($v) {
		return "<p>" . e($v) . "</p>";
	});

	// 链接
	$show->field('outlink')->link();

	// 标签(逗号分隔)
	$show->field('tags')->unescape()->explode()->label();

	// 标签动态数据
	$show->field('aid')->unescape()->as(function ($v) {
		$list = Articles::whereIn('id', explode(',', $v))
			->where('type', 1)
			->pluck('title');
		return $list;
	})->label();

	// 状态下拉
	$show->field('status')->using([1 => '正常', 2 => '禁用']);

	// 缩略图尺寸
	$show->field('image')->image('', 100, 100);

	// 动态标签及返回值
	$read = Read::findOrFail($id);
	$aidLabel = match ($read->type) {
		1 => '新闻',
		2 => '快讯',
		3 => '专题',
		default => ''
	};
	return Show::make($id, new Read(), function (Show $show) use($aidLabel) {
		$show->field('aid',$aidLabel)->as(function ($value) {
			/** @var \App\Models\Read $this */
			if ($this->type != 3) { // 文章或快讯
					return \App\Models\Articles::where('id', $value)->value('title');
			} else { // 专题
					return \App\Models\Topic::where('id', $value)->value('title');
			}
		});
	});
	
	// 树形结构
	use Dcat\Admin\Widgets\Tree;
	// use Dcat\Admin\Support\Helper;
	$show->field('role_ids')->unescape()->as(function ($role_ids) {
		$WhRoleMenuModel = new WhRoleMenu();
		$whRoleMenuList = $WhRoleMenuModel->where('type', 2)->select('id', 'pid', 'name')->get();
		$tree = Tree::make($whRoleMenuList);
		$tree->setParentColumn('pid');
		$tree->check($role_ids);
		// Dcat系统写法
		// $keyName = $permissionModel->getKeyName();
		// $tree->check(
		//     array_column(Helper::array($permission), $keyName)
		// );
		return $tree->render();
	});
	```