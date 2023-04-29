---
layout: post
title: fastadmin 列长度限制
category: JavaScript
keywords: 
tags: 常识 JavaScript
description: 
---

```
{
    field: 'category_name',
    title: __('关联行业类目'),
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

参考链接：[https://ask.fastadmin.net/question/22641.html](https://ask.fastadmin.net/question/22641.html)