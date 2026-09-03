---
layout: post
title: node-sass 对应版本
category: Tool
keywords: Common Tool
tags: Common Tool
description: 
---

> 在使用 node-sass 时，你可能会遇到安装 node-sass 时出现各种错误的情况。在本文中，我们将探讨一些常见的 node-sass 安装错误，以及如何解决它们。无论你是初学者还是有经验的开发者，本文都将为你提供有用的信息和技巧，帮助你成功安装 node-sass。

问题描述:

众所周知，node-sass 是我们开发中很常见的依赖包，但同时也是安装时间冗长和最常见到报错的依赖。安装 node-sass 时你可能因为 npm 源速度慢安装失败，也有可能因为 node 版本与 node-sass 版本不兼容而安装失败。而报错最多的无非以下两种情况

![image.png](https://blog.alonesky.com/storage/article/2025/01/18/NAkubwEzjWDhf1ntWpV70ts1S41TSfVyx5Mbw6h2.png)

![image.png](https://blog.alonesky.com/storage/article/2025/01/18/0Hf18QyqYSMJJKxaV9L5P3QM3XzWzMZN346MZIs8.png)

原因分析：

其实以上两张图的报错归根结底都是因为 node 版本和 node-sass 版本不兼容的问题，目前网上最多的解决办法就是先降低 node-sass 的版本，再选择其它的版本进行安装，但这个方法并不适用于所有人，其实最好的办法就是参照 node-sass 官方文档查看自己 node 版本对应的 node-sass 版本，查看地址：[https://www.npmjs.com/package/node-sass](https://www.npmjs.com/package/node-sass)

![image.png](https://blog.alonesky.com/storage/article/2025/01/18/WKBzI27Mb07Rvus4xvQyH8WC09hDN1zkiHdJIa6T.png)

解决方案：

我们可以根据上图找到其中对应的版本，查看自己当前的 node 版本号，然后删除项目中的 node_modules 包，最后卸载当前版本的 node-sass 再重新安装相应的版本即可。

查看node版本
```shell
node -v
```

卸载命令
```shell
npm uninstall node-sass
cnpm uninstall node-sass
```

安装对应版本
```shell
npm install node-sass@4.14.1
cnpm install node-sass@4.14.1
```

别急，还没完，有很多同学到了这一步依旧会报错，这个时候我们可以来一波反向操作，同时降低 node 及 node-sass 的版本。我这边安装的 node 版本是 14.18.2，node-sass 版本是 4.14.1，经本人自测，完美运行。

操作步骤：

先卸载当前的 node（在电脑的控制面板中找到卸载程序右键 node 将其卸载即可）；
去官网寻找匹配的 node 版本([node以往版本的下载地址](https://nodejs.org/dist/))，如下图 
安装完 node 后，记得将项目中的 node_modules 包删了，然后再重新下载运行项目即可。

![image.png](https://blog.alonesky.com/storage/article/2025/01/18/DFK8NMi2uCSgUfKQ1856bEbu1Mlq0SVfHwMW9hdT.png)

最后附上成功运行图

![image.png](https://blog.alonesky.com/storage/article/2025/01/18/tDvM3rOGfODPu1YCghSiTxBY2Ntc1wNzTuPEQEdo.png)

原文链接：[https://blog.csdn.net/Shids_/article/details/126406087](https://blog.csdn.net/Shids_/article/details/126406087)