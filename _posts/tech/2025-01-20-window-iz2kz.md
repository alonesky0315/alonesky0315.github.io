---
layout: post
title: window电脑右上角的了解此图片怎么去掉
category: Tool
keywords: Common Tool
tags: Common Tool
description: 
---

> window电脑右上角的了解此图片怎么去掉

一、 方法一修改注册表一劳永逸

1. 修改注册表。   
按Win+R打开运行窗口，输入regedit打开注册表编辑器；依次展开到以下路径：HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\HideDesktopIcons\NewStartPanel；
2. 新建一个“DWORD（32位）值   
右键点击“NewStartPanel”,选择新建一个“DWORD（32位）值”,命名为“{2cc5ca98-6485-489a-920e-b3e88a6ccce3}”,双击这个新值，将其数值数据修改为1；关闭注册表编辑器，在桌面空白处右键刷新即可去除“了解此图片”图标

二、 方法二使用纯色背景 

1. 更改个性化设置。
打开开始设置，选择个性化；点击“背景”，选择“个性化设置背景”；将“Windows聚焦”更改为其他选项，如图片或纯色。