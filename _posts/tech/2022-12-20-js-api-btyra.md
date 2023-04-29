---
layout: post
title: 网站百度地图提示：您所使用的地图JS API版本过低
category: JavaScript
keywords: 
tags: 常识 JavaScript 插件
description: 
---

从代码里面找到链接

```
http://api.map.baidu.com/api?key=&v=1.1&services=true
http://api.map.baidu.com/api?v=1.2&services=false
```

然后替换成这个链接
```
https://api.map.baidu.com/api?v=3.0&ak=你的秘钥
```
秘钥进这里注册一下就可以获取

[http://lbsyun.baidu.com/apiconsole/key?application=key](http://lbsyun.baidu.com/apiconsole/key?application=key)

可以解决如下问题：

您所使用的地图JS API版本过低,已不再维护的解决方案

百度地图提示JS API版本过低

地图js+api版本过低怎么解决

之前网页上放的百度地图发现提示:JS API版本过低

您所使用的地图JS API版本过低,已不再维护,需要升级

原文链接：[https://blog.csdn.net/lccee/article/details/128163973](https://blog.csdn.net/lccee/article/details/128163973)