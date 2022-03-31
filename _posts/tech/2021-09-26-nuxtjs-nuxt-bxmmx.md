---
layout: post
title: Nuxt.js 速成（含使用宝塔实现nuxt项目部署）
category: JavaScript
keywords: 
tags: JavaScript
description: 
---

> 创建项目

```
npx create-nuxt-app 项目名称
```
根据需要选择配置和依赖 
![image.png](https://blog.alonesky.com/storage/article/2021/09/26/Xqeiu0Akn9SJEWznSx5HcoQFQEZLL6eUmSeEv6r7.png)

创建成功

![image.png](https://blog.alonesky.com/storage/article/2021/09/26/xZPMP4cBrToj0V4tpDSPmL9oHkGP3lBENbG4ixWw.png)
> 启动项目

进入项目目录
```
cd 项目名称 
```
启动项目
```
npm run dev
```
![image.png](https://blog.alonesky.com/storage/article/2021/09/26/Ji87KC53fgfwQiMVu4c6aR3lSHwobaBodvNDJxFl.png)

浏览器访问 http://localhost:3000
![image.png](https://blog.alonesky.com/storage/article/2021/09/26/U2Y8rGeyDZRavN10xzvgbEiBTQhYTcYcoimUsc82.png)
> 部署项目

1. 云服务器上安装PM2管理器 
若未安装宝塔，参考链接安装  https://blog.csdn.net/weixin_41192489/article/details/109668849
登录宝塔管理面板，在软件商店中搜索安装（安装好后，系统里便有了node.js  npm nvm 和 pm2）
![image.png](https://blog.alonesky.com/storage/article/2021/09/26/bwPtv9Id7Ud0n70GpqL0cHpeJ6VFFijlPzUGwJfP.png)
2. 防火墙放行3000端口
	宝塔中在安全菜单中，防火墙放行3000端口（nuxt项目启动后，使用的3000端口）
![image.png](https://blog.alonesky.com/storage/article/2021/09/26/oCND006Xjlh1dyDH5EXhbbeplN4BHeTa62yl7DT8.png)
3. 在项目的package.json中添加端口配置

	位置与scripts同级
```
"config": {
    "nuxt": {
        "host": "0.0.0.0",
        "port": 3000
    }
},
```
4. 打包项目
```
npm run build
```
5. 上传项目包到服务器

	在云服务器中，以项目名称新建文件夹，如itdic，将 .nuxt、static、nuxt.config.js、package.json、package-lock.json上传到该文件夹中

	.nuxt 是隐藏文件，在xftp界面不显示

6. 安装依赖

	登录远程服务器命令行，进入项目包的目录
```
 cd /vue
```
安装依赖
```
npm i
```
7. 启动项目

	打开PM2管理器的设置,在项目列表中添加项目
![image.png](https://blog.alonesky.com/storage/article/2021/09/26/yTGSrjJogbLAZqwMsh6PkocaBkZVq0VCgDHHcci8.png)

保存后，项目会自动启动。

访问 http://ip:3000/ 【云服务的外网IP:3000】页面正常显示，即部署成功。

原文链接：[https://blog.csdn.net/weixin_41192489/article/details/117553848](https://blog.csdn.net/weixin_41192489/article/details/117553848)