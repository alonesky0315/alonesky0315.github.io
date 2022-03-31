---
layout: post
title: Fastadmin导出表格
category: JavaScript
keywords: 
tags: 常识 JavaScript
description: 
---

```
// 老版本
table.bootstrapTable({
  ...
    showExport: true,
    exportTypes:['excel'],
    exportOptions: {
        type: 'excel', 
        ignoreColumn: [0, -1], // 忽略第一列和最后一列
        // ignoreColumn: [0, 'operate'],忽略第一列和操作栏
        onMsoNumberFormat: function(cell, row, col) {
            return (row > 0 && col == 0) ? '\\@' : '';
        }
    }
...
) 
// 新版本
table.bootstrapTable({
  ...
    showExport: true,
    exportTypes:['excel'],
    exportOptions: {
        ignoreColumn: [0, -1], // 忽略第一列和最后一列
        // ignoreColumn: [0, 'operate'],忽略第一列和操作栏
        mso: {
            //修复导出数字不显示为科学计数法
            onMsoNumberFormat: function (cell, row, col) {
                return !isNaN($(cell).text())?'\\@':'';
            }
        }
    },
...
)
```
> 备注
> 
>  "\@" 强制为文本格式    
> "0" 数字 无小数         
> "0\.000" 数字 三位小数          
> "0%" 百分比 无小数       
> "Percent" 百分比 两位小数        

参考网址：[https://ask.fastadmin.net/question/1346.html](https://ask.fastadmin.net/question/1346.html)