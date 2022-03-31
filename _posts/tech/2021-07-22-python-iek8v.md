---
layout: post
title: Python 虚拟环境
category: Python
keywords: 
tags: 常识 Python
description: 
---

> pipenv 就是 pip 和 virtualenv 的结合体。

```
# 安装
pip install pipenv
# 创建虚拟环境
pipenv install
#安装库
pipenv install xxx
#进入虚拟环境
pipenv shell
#退出虚拟环境
exit
#删除指定库
pipenv uninstall xxx
#删除所有库
pipenv uninstall --all
#升级库
pipenv update
#查看库信息
pipenv open xxx
#获取本地工程路径
pipenv --where
#获取虚拟环境路径
pipenv --venv
#检查库的依赖关系
pipenv graph
#检查库的安全性
pipenv check
#删除虚拟环
pipenv --rm
```
参考链接: [https://mp.weixin.qq.com/s/Ctf4cO32OovV3DzlKC87tw](https://mp.weixin.qq.com/s/Ctf4cO32OovV3DzlKC87tw)