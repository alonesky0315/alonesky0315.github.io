---
layout: post
title: Electron 封装程序
category: 知识库
keywords: 
tags: 常识 工具
description: 
---

#### electron 封装程序

//使用淘宝NPM开发的cnpm进行package的安装  

//首先安装cnpm 
```
npm install -g cnpm --registry=https://registry.npm.taobao.org //使用cnpm进行安装 cnpm install -g
```
electron （全局安装electron）

```
cnpm install （全部安装）
```

//启动   

```
npm start
```
//使用electron-packager打包工具，首先全局安装一下：

```
npm install electron-packager -g
```
//执行打包

```
electron-packager <location of project> <name of project> <platform> <architecture> <electron version> <optional options>
* location of project：项目所在路径
* name of project：打包的项目名字
* platform：确定了你要构建哪个平台的应用（Windows、Mac 还是 Linux）
* architecture：决定了使用 x86 还是 x64 还是两个架构都用
* electron version：electron 的版本
* optional options：可选选项
//ex: electron-packager . hello --out=../exe --icon=./favicon.ico
//os系统 ex:electron-packager . 'hello' --platform=darwin --arch=x64 --icon=favicon.ico --out=./exe --asar --app-version=2.0.1 --ignore=\"(dist|src|docs|.gitignore|LICENSE|README.md|webpack.config*|node_modules)\
//win位 ex:electron-packager . 'hello' --platform=x64(win32) --arch=x64(ia32) --icon=favicon.ico --out=./exe --asar --app-version=2.0.1 --ignore=\"(dist|src|docs|.gitignore|LICENSE|README.md|webpack.config.js|node_modules)\
//Linux ex:electron-packager . 'hello' --platform=linux --arch=x64 --out=./exe --asar --app-version=2.0.1 --ignore=\"(dist|src|docs|.gitignore|LICENSE|README.md|webpack.config.js|node_modules)\
```
//一次性把所有的操作系统都打包一遍

```
electron-packager . hello --all --out ../exe --icon=./favicon.ico
```
//app.asar解压：

先通过npm安装asar（前提是你要安装了npm管理器）：

```
npm install -g asar
```
然后通过asar解压：
```
asar extract app.asar destfolder
```
应用的核心部分是resource文件下的app.asar

//打包和系统不同的安装包

//设置淘宝镜像

```
set electron_mirror=https://npm.taobao.org/mirrors/electron/
```
//执行打包命令

```
electron-packager . hello --all --out ../exe --icon=./favicon.ico
```