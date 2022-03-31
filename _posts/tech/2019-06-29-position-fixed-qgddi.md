---
layout: post
title: Position: fixed;定位后不能滚动
category: HTML
keywords: 
tags: HTML
description: 
---

### position: fixed;定位后不能滚动
> top:必须加上这个才能滚动   
> bottom:距离底部位置   
> overflow-y: auto/scroll;/overflow-x: auto/scroll;auto:自动,scroll:滚动,hidden:隐藏   
```
.category ul {
    position: fixed;
    top: 0;
    bottom: 45px;
    overflow-y: auto;
}
```