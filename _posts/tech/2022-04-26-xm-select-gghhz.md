---
layout: post
title: xm-select 基本使用
category: JavaScript
keywords: 
tags: 常识 JavaScript
description: 
---

```
// 页面
<script src="https://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
<script src="/static/plugins/layui/layui.js" ></script>
<script src="/static/plugins/layui/xm-select.js" ></script>
<div class="layui-form-item">
    <label class="layui-form-label">管辖小区<a style="color: red">*</a></label>
    <div class="layui-input-block">
        <div class="xm-select"></div>
    </div>
</div>
<script>
// 添加
var xm_select=xmSelect.render({
    el: '.xm-select',
    data:[],
    filterable: true,
    paging: true,
})
$.getJSON("/admin/service_station/xm_select_search", function(result) {
    if(result.status==200){
        xm_select.update({
            data: result.data,
            autoRow: true,
        })
    }else{
        return []
    }
})
// 修改先设置默认值
var xm_select=xmSelect.render({
    el: '.xm-select',
    data:[],
    filterable: true,
    paging: true,
    initValue: [{$vills}]
})
$.getJSON("/admin/service_station/xm_select_search", function(result) {
    if(result.status==200){
        xm_select.update({
            data: result.data,
            autoRow: true,
        })
    }else{
        return []
    }
})
</script>

// 控制器
public function xm_select_search()
{
    $community = Db::name('community')
        ->field('id,sheng,city,area,vill')
        ->where('status', '=', 1)
        ->select();
    $community_data = [];
    foreach ($community as $key => $item) {
        $community_data[$key] = [
            'name' => $item['vill'],
            'value' => $item['id']
        ];
    }
    return json(['status' => 200, 'msg' => '成功', 'data' => $community_data]);
}

```

xm-select手册：https://maplemei.gitee.io/xm-select/#/basic/initValue