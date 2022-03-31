---
layout: post
title: Linux常用命令
category: Linux
keywords: 
tags: 常识 Linux
description: 
---

#### Linux 常用命令
#重新加载配置文件
#/usr/sbin/nginx -s reload
#nginx (start|stop|restart)
#/etc/init.d/nginx (start|stop|restart)
#去掉保护
#chattr -i .user.ini
#查看进程
#ps aux | grep nginx
#查看端口开放
#lsof -i:8080
#显示全部打开的端口，根据显示close/open确定端口是否打开
#nmap ip
#nmap 192.168.1.105
#测试端口是否通畅
#nmap ip -p port
#nmap 192.168.1.105 -p 80
#端口未打开返回状态为非0
#nc -v host port
#nc -v 192.168.1.105 80
#脚本方式检测端口是否开放
#nc -w 10  192.168.1.105 80 && echo ok ||echo no
#更改文件夹权限
#chmod -R 777 /wwwroot/default
#chown改变所有者
#chown -R jun:www /wwwroot
#查看nginx运行状态
#systemctl status nginx.service
#编辑文件
#vi /etc/nginx/nginx.conf
#apt 更新软件列表
#apt update
#apt 更新已安装软件
#apt upgrade
#自动移除过期文件
#apt autoremove
#查找文件
#find / -name "fastcgi-php.conf"
#列出文件
#ls
#列出全部文件(包含隐藏文件)
#ls -a
#查看文件权限
#ls -l /file
#创建文件
#touch readme
#创建文件夹
#mkdir file
#切换用户
#su jun
#ln -s 源目录 软连接目录