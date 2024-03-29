---
layout: post
title: Windows11家庭中文版系统安装Docker
category: Tool
keywords: Common Tool
tags: Common Tool
description: 
---

1. 启动本机虚拟机管理程序Hyper-V,找到控制面板--程序--程序和功能--启用或关闭windows功能（或者电脑直接搜索启用或关闭windows功能） ，勾选Hyper-V。（如果找不到 Hyper-V，请跳转到 4 步骤开始）
 ![image.png](https://blog.alonesky.com/storage/article/2023/11/13/Nq053vPqS3evjIwPoEhYvDnGPK6xfBogMolxCaQQ.png)
2. 在Windows 操作系统中执行命令启用 Microsoft Hyper-V 虚拟化技术。 
```
dism.exe /Online /Enable-Feature:Microsoft-Hyper-V /All
```
3. 设置 Windows 操作系统中的 Hyper-V 启动类型。具体来说，它会将 Hypervisor 的启动类型设置为 "auto"，这意味着在系统启动时自动启动 Hyper-V
```
bcdedit /set hypervisorlaunchtype auto
```
4. 如果你的系统跟我一样是window11家庭中文版，则会找不到Hyper-Vr，这时则需要自己创建，讲下述代码复制在txt文本里，并重命名为Hyper.cmd，，右键以管理员方式运行，最后输入“Y”重启电脑后即可。
```
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```
5. 下载docker安装   
 Docker官网：[https://www.docker.com/get-started/](https://www.docker.com/get-started/)

备注：
如果重启电脑后又出现下图报错，则需要升级wsl
![image.png](https://blog.alonesky.com/storage/article/2023/11/13/QvIqjsL1C9Qc0AXu7Towp8zZeSxxgpnoOpWtcHZX.png)

以管理员身份运行终端执行`wsl --update`

原文链接：[https://blog.csdn.net/qq_42262818/article/details/132023279](https://blog.csdn.net/qq_42262818/article/details/132023279)