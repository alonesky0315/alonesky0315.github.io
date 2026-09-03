---
layout: post
title: 利用string-replace-loader替换js或css文件
category: JavaScript
keywords: Common JavaScript
tags: Common JavaScript
description: 
---

#### 利用string-replace-loader替换js或css文件
1. 安装string-replace-loader
```shell
npm install string-replace-loader --save-dev
```
2. 配置webpack.mix.js
```js
const mix = require('laravel-mix');
let config = {
  ...
  module: {
    rules: [
      // 替换 simplemde 中的font-awesome
      {
        test: /simplemde\.min\.js$/,
        use: [
          {
            loader: "string-replace-loader",
            options: {
              search:
                "https://maxcdn.bootstrapcdn.com/font-awesome/latest",
              replace: "/font-awesome",
              flags: "g",
            },
          },
          {
            loader: "string-replace-loader",
            options: {
              // 替换 aff 文件的 CDN 路径
              search: "https://cdn.jsdelivr.net/codemirror.spell-checker/latest",
              replace: "/codemirror-spell-checker",
              flags: "g",
            },
          }
        ]
      }
    ]
  }
};
mix.webpackConfig(config);
```
3. 将替换的文件夹放在本地可访问的目录
```
font-awesome codemirror-spell-checker
```