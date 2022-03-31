---
layout: post
title: 如何让Git通过Windows防火墙
category: 工具
keywords: 
tags: 常识 工具
description: 
---

执行 `git pull`出现fatal: unable to access

添加规则,git-remote-https.exe因为我通过HTTPS进行身份验证,而不是SSH

位置：程序安装目录\Git\mingw64\libexec\git-core\git-remote-https.exe.

禁用了规则git.exe,sh.exe,ssh.exe,和bash.exe,仍然一切正常

原文链接：[https://qa.1r1g.com/sf/ask/1840839101](https://qa.1r1g.com/sf/ask/1840839101/)