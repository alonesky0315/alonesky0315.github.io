---
layout: post
title: go mod download不能下载到go get golang.org/x 的包解决方案
category: GO
keywords: 
tags: 常识
description: 
---

```
> make deps          
go mod download
go: github.com/BurntSushi/toml@v0.3.1: Get "https://proxy.golang.org/github.com/%21burnt%21sushi/toml/@v/v0.3.1.mod": dial tcp 172.217.24.17:443: i/o timeout
Makefile:65: recipe for target 'deps' failed
make: *** [deps] Error 1
```

直接执行代理：

```
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.io,direct
```

原文链接：[https://blog.csdn.net/hcu5555/article/details/106076141](https://blog.csdn.net/hcu5555/article/details/106076141)