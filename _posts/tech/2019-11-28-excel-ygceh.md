---
layout: post
title: Excel公式
category: 知识库
keywords: 
tags: 常识
description: 
---

### Excel公式
```
1.时间
NOW() //当前日期
TIME(20,12,16)  //20:12PM
2.计算工龄
=DATEDIF(A1,B1"y")&"年"&ROUNDUP(DATEDIF(A1,B1,"ym"),1)&"个月"&ROUNDUP(DATEDIF(A1,B1,"md"),1)&"日"
3.利用求余MOD()、TEXT()计算加班时间
MOD(被除数，除数)
MOD(A2-A1,1) //计算时间
TEXT(MOD(A2-A1,1),"h小时mm分钟") //文本格式 2小时20分钟
TEXT(MOD(A2-A1,1),"h:mm") //文本格式 2:20
4.
```
![Uploading ...]()