---
layout: post
title: Fastadmin基础知识（三）
category: JavaScript
keywords: 
tags: 常识 JavaScript
description: 
---

1. simditor编辑器图片超出样式
```
.simditor-body img{
		max-width: 98%;
		height: auto;
}
```

2. 固定列
```
table.bootstrapTable({
		url: $.fn.bootstrapTable.defaults.extend.index_url,
		pk: 'id',
		sortName: 'id',
		fixedColumns: true,// 是否固定列，移除或false为不固定
		fixedRightNumber: 1,// 右侧几列
		showToggle:false, // 是否显示切换功能，移除或true为打开
		showColumns:false, // 是否显示列功能，移除或true为打开
		showExport: true,// 是否显示导出，移除或false为不固定
})
```

3. 自定义快速搜索placeholder文本
```
$.fn.bootstrapTable.locales[Table.defaults.locale]['formatSearch'] = function(){return "自定义placeholder文本";};
```

4. 通用搜索添加下拉筛选
```
{
    field: 'district',
    title: __('District'),
    addClass: "selectpage",
    extend: "data-source='business/getDistrictList' data-field='name'"
}
public function getDistrictList(){
    $regionModel=new Region();
    $list=$regionModel->field('id,mergename as name')->select();
    $result = array("total" => count($list), "rows" => $list);
    return json($result);
}
```

5. 关联搜索

```
// Controller
protected $relationSearch=true;
protected $searchFields='id,title,shop.name';
$list = $this->model
    ->with('shop')
    ->where($where)
    ->order($sort, $order)
    ->limit($offset, $limit)
    ->select();

// Model
public function shop()
{
    return $this->belongsTo('shop','id')->setEagerlyType(0);
}

// Js
{field: 'shop.name', title: __('Shop'), operate: 'LIKE %...%',}
```

参考网址：

关联模型搜索：[https://ask.fastadmin.net/article/7542.html](https://ask.fastadmin.net/article/7542.html)