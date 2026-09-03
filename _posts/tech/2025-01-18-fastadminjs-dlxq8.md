---
layout: post
title: fastadmin中的js怎么根据变量控制列的显示隐藏
category: JavaScript
keywords: Common JavaScript
tags: Common JavaScript
description: 
---

#### fastadmin中的js怎么根据变量控制列的显示隐藏

方式一
```js
if(Config.typs == 5){
  stvisible = false;
}

// 初始化表格
table.bootstrapTable({
    url: $.fn.bootstrapTable.defaults.extend.index_url,
    pk: 'id',
    sortName: 'id',
    sortOrder:'asc',
    columns: [
        [
            {field: 'name', title: __('Name'),visible: stvisible },
        ]
    ]
})
```
方式二
```php
$result = array("total" => $total, "rows" => $list, "extend" => ['money' => 1024]);
return json($result);
```
```js
// 当表格数据加载完成时
table.on('load-success.bs.table', function (e, data) {
    // 获取服务端的JSON数据
    console.log(data);
    if(data.extend=='money'){
        table.bootstrapTable('showColumn', 'money');
    }else{
        table.bootstrapTable('hideColumn', 'money');
    }
});
```

原文链接：[https://ask.fastadmin.net/question/7031.html](https://ask.fastadmin.net/question/7031.html)