---
layout: post
title: fastadmin 列长度限制
category: JavaScript
keywords: 
tags: 常识 JavaScript
description: 
---

fastadmin 列长度限制

1. 在每个js中添加

```
{
    field: 'title',
    title: __('标题'),
    cellStyle: {
        css: {
            "max-width": "300px",
            "white-space": "nowrap",
            "overflow": "hidden",
            "text-overflow": "ellipsis"
        }
    },
    operate: 'LIKE',
    formatter: Controller.api.formCateName
},

api: {
    bindevent: function () {
        formCateName:function(value,row,index, field){
            var span=document.createElement('span');
            span.setAttribute('title',value);
            span.innerHTML = value;
            return span.outerHTML;
        },
    }
}
```

2. 在 require-table.js中修改content

```
{
    field: 'title',
    title: __('标题'),
    operate: 'LIKE',
    formatter: Table.api.formatter.content
},

api: {
    ...
    formatter: {
        ...
        content: function (value, row, index) {
            var width = this.width != undefined ? (this.width.match(/^\d+$/) ? this.width + "px" : this.width) : "250px";
            return "<div title='" + value + "' style='white-space: nowrap; text-overflow:ellipsis; overflow: hidden; max-width:" + width + ";'>" + value + "</div>";
        },
        ...
    }
    ...
}
```

参考链接：[https://ask.fastadmin.net/question/22641.html](https://ask.fastadmin.net/question/22641.html)