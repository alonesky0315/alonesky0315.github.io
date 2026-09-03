---
layout: post
title: Hexo 静态博客入门教程
category: Linux
keywords: Common Linux
tags: Common Linux
description: 
---

> 通过 Hexo 博客框架快速生成静态博客

1. 安装 Node.js
    ```
    curl -sL https://rpm.nodesource.com/setup_16.x | sudo bash - && sudo yum clean all && sudo yum makecache  && sudo yum install -y gcc-c++ make && sudo yum install -y nodejs
    ```
2. 换源
    ```
    npm config set registry https://registry.npmmirror.com/
    ```
3. 安装 Hexo
    ```
    npm install -g hexo-cli
    ```
4. 创建目录并设置权限
    ```
    mkdir /hexo && cd /hexo && chmod 777 /hexo
    ```
5. 初始化博客项目
    ```
    hexo init blog --no-clone
    ```
5. 生成博客的静态文件
    ```
    hexo generate (简写: hexo g)
    ```
6. 启动博客
    ```
    hexo server (简写: hexo s)
    ```
7. 创建/hexo/blog/source/_posts/first.md文章模板
    ```
    hexo new first
    ```
8. 编写 /hexo/blog/source/_posts/first.md 文件
    ```
    ---
    title: first
    date: 2025-02-27 11:41:57
    tags:
    ---

    This is my first post.
    ```
9. 重新生成静态文件 
    ```
    hexo generate -d
    ```

常用命令：   
```
// 查看Node的版本信息
node -v

// 查看npm的版本信息
npm -v

// 安装Git
yum install git -y

// 查看Git的版本
git --version

// 查看Hexo 的版本及其依赖信息
hexo -v

// 清理缓存文件
hexo clean

// 生成静态文件
hexo generate

// 启动Hexo
hexo server

// 将本地生成的静态文件上传到指定的远程目标
hexo deploy (简写: hexo d)

// github安装主题
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly

// npm安装主题
npm i hexo-theme-butterfly

// gitee安装主题
git clone -b master https://gitee.com/immyw/hexo-theme-butterfly.git themes/butterfly

// 修改 Hexo 根目录下的 _config.yml，把主題改为 butterfly
theme: butterfly

// 安裝pug 以及 stylus 渲染器
npm install hexo-renderer-pug hexo-renderer-stylus --save
```

参考链接：[https://cloud.tencent.com/lab/courseDetail/915869039067641](https://cloud.tencent.com/lab/courseDetail/915869039067641)