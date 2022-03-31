---
layout: post
title: IIS重构php环境
category: 知识库
keywords: 
tags: 常识 工具
description: 
---

#### iis重构php环境
##### 添加mysql服务到服务管理中
* windows+r 打开cmd命令窗口  或者在 安装目录下的bin目录 按住shift 鼠标右键 从此处打开命令窗口   
* 进入mysql的bin目录
* 添加mysql服务到服务管理中 `mysqld.exe -install`    
* mysql服务卸载命令：`mysqld.exe -remove` （科普不是必须操作的）  
注意：利用cmd命令添加和删除服务时，必须要用管理员权限去操作

##### MySQL忘记密码
* 进入mysql的bin目录  
* 停止服务 `net stop mysql`  
* 启动 MySQL 服务的时候跳过权限表认证 `mysqld --skip-grant-tables`  
注意：这时候，刚刚打开的 cmd 窗口已经不能使用了。重新再 bin 目录下打开一个新的 cmd 窗口进行下面的操作 
* 输入 `mysql` 回车 
* 连接权限数据库：`use mysql`
* 修改数据库连接密码： `update user set password=password("123456") where user="root";`//注意这里最后的分号一定不能丢
* 刷新权限（必须步骤）：`flush privileges;`（注意分号）
* 退出mysql： `quit` //（这里没有分号）`exit` 也可以
* 关闭无密码进入：`mysqladmin -u root -p shutdown` 
* 重启：`mysql net start mysql`

##### IIS7.5开启FastCGI的配置方法
控制面板→程序和功能→打开或关闭Windows功能，在打开的窗口中依次展开Internet信息服务→万维网服务→应用程序开发功能→勾选CGI选项即可   
参考链接： 
添加mysql服务：[https://jingyan.baidu.com/article/4853e1e57f13721909f7261a.html](https://jingyan.baidu.com/article/4853e1e57f13721909f7261a.html)   
MySQL忘记密码：[https://www.cnblogs.com/rmxd/p/11236736.html](https://www.cnblogs.com/rmxd/p/11236736.html)    
IIS7.5开启FastCGI的配置方法：[https://www.jb51.net/article/51421.htm](https://www.jb51.net/article/51421.htm)