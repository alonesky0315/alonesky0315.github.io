---
layout: post
title: VS Code使用sftp远程修改文件
category: Common
keywords: Common Tool
tags: Common Tool
description: 
---

### VS Code使用sftp远程修改文件

1. 下载插件SFTP
![image.png](https://blog.alonesky.com/storage/article/2023/08/18/OZAEk539jzXjBxUnmKrkTmmeqGXSdQ5NtBw6QHPt.png)
2. 创建任意一个文件夹   
sftp插件是在文件夹下生效的, 因为你要在文件夹下进行sftp的配置,以及打开远程文件  
快捷键: Ctrl+Shift+P 
![image.png](https://blog.alonesky.com/storage/article/2023/08/18/3g1nL4Iezd0y8nX7gCly2fJMzWdpoe47AKHrirS4.png)
3. 配置远程连接配置   
```
{
    "name": "Server",
    "host": "localhost",
    "protocol": "sftp",
    "port": 22,
    "username": "root",
    "password": "root",
    "remotePath": "/site",
    "uploadOnSave": false,
    "downloadOnOpen": false,
    "watcher": {
        "files": "**/*",
        "autoUpload": true,
        "autoDelete": true
    },
    "ignore": [
        "node_modules",
        ".vscode",
        ".git",
        ".DS_Store"
    ]
}
``` 
4. 打开远程连接视图
![image.png](https://blog.alonesky.com/storage/article/2023/08/18/jCWbCEATdxPK8PHPTqKXsDQTSTEoRKTAHLbZlvra.png)

原文链接：[https://blog.csdn.net/weixin_43918355/article/details/115103645](https://blog.csdn.net/weixin_43918355/article/details/115103645)