---
layout: post
title: Layui数据表格动态表头
category: 知识库
keywords: 
tags: 常识 JavaScript HTML PHP
description: 
---

html代码

```
<table id="table" lay-filter="table"></table>
```

js代码

```
var fuck='<{$tou|raw}>';
// console.log(fuck)
var header=[JSON.parse(fuck)];
// console.log(header)
var order_table = table.render({
    elem: '#table',
    url:'/admin/order/get_cen',
    title: '订单列表',
    method:'post',
    toolbar:true,
    defaultToolbar:['filter', 'exports'],
    totalRow: true,
    text:{none:'暂无数据'},
    height:'full-350',// 固定表头
    cols: header,
    done: function(res, curr, count){
        let field_array=res.data;
        field_array.forEach(function(item,key,array) {
            $('.layui-table-total .laytable-cell-2-'+item.key).text(
						parseInt($('.layui-table-total .laytable-cell-2-'+item.key).text()));
        });
        // $('.layui-table-total ').attr();
    }
});
$('#btnSearch').on('click',function () {
    $.ajax({
        url: '/admin/order/get_cen_head',
        type: 'post',
        dataType: 'json',
        data:{
            vill:$('#vill').val(),
            times:$('#times').val(),
            group:$('#group').val(),
        },
        success: function (res) {
            table.render({
                elem: '#table',
                url:'/admin/order/get_cen',
                title: '订单列表',
                method:'post',
                where:{
                    vill:$('#vill').val(),
                    times:$('#times').val(),
                    group:$('#group').val(),
                },
                toolbar:true,
                defaultToolbar:['filter', 'exports'],
                totalRow: true,
                text:{none:'暂无数据'},
                height:'full-350',
                cols: [res.head]
            });
        }
    })
})
```

PHP代码

```
$head=[{"field":"addr", "title":"区域", "width":"170", "sort":true, "totalRowText":"合计"}];
json($head);
```

参考网址：[https://wenku.baidu.com/view/79dcfd69862458fb770bf78a6529647d2728347e.html
](https://wenku.baidu.com/view/79dcfd69862458fb770bf78a6529647d2728347e.html
)