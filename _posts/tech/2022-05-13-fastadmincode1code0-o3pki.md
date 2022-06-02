---
layout: post
title: Fastadmin离线安装插件提示请从官网渠道下载插件压缩包(code:1)(code:0)
category: PHP
keywords: 
tags: 常识
description: 
---

1. 修改config.php文件

```
'unknownsources'=> true,
```

2. 修改/vendor/karsonzhang/fastadmin-addons/src/addons下的service.php文件

```
// 注释此句
// 压缩包验证、版本依赖判断
Service::valid($params);
```

参考链接：[https://blog.csdn.net/weixin_43652106/article/details/119705806](https://blog.csdn.net/weixin_43652106/article/details/119705806)