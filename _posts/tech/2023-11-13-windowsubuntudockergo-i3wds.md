---
layout: post
title: 在Windows子系统Ubuntu搭建Docker部署Go项目
category: GO
keywords: Common Linux
tags: Common Linux
description: 
---

1. 进入Windows商店Microsoft Store搜索Ubuntu找到Ubuntu版本点击下载安装
2. 打开 "Ubuntu" 应用，完成初始化配置。
3. 在 Ubuntu 终端中更新系统的软件包列表
```
sudo apt update
```
4. 在 Ubuntu 终端中安装Docker
```
sudo apt install docker
# 查看docker版本确定是否安装成功
docker –version
```
5. 在 Ubuntu 终端中创建文件目录
```
mkdir /wwwroot/project
```
6. 在 Ubuntu 终端中复制Windows系统中的docker-compose.yml手动导入数据库
```
cp /mnt/e/project/docker-compose.yml /wwwroot/project/docker-compose.yml
# 进入目录并执行拉取docker镜像代码
cd /wwwroot/project/ && docker compose up
```
7. 在 Ubuntu 终端中安装net-tools查看网卡IP
```
apt install net-tools
ifconfig
# eth0:网卡ip
```
8. 安装nginx
```
apt install nginx
# 查看nginx服务状态
service nginx status
# 查看nginx安装位置(/etc/nginx/)
whereis nginx
# 复制默认配置添加项目
cp /etc/nginx/sites-enabled/default /etc/nginx/sites-enabled/i.project.com
# 重启nginx
service nginx restart
# 查看nginx启动报错原因，启动成功可跳过此步
systemctl status nginx.service
```
9. 修改hosts文件(/etc/hosts文件与本地Windows系统同步的，修改本地C:\Windows\System32\drivers\etc\hosts即可)
```
eth0 Ip   i.project.com
```

10. 打开Windows PowerShell或命令提示符配置WSL 2为默认版本：
```
wsl –set-default-version 2
```

备注：
```
# 查看端口占用
sudo lsof -i :3305
# 结束占用端口的PID进程
sudo kill -9 占用端口的PID
# 备份源
sudo cp /etc/apt/sources.list   /etc/apt/sources.list.bak
# 修改软件源(按ESC键进入指令模式，输入"wq"保存并退出,提示只读可输入"wq!"强制退出)
vi /etc/apt/sources.list

deb https://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb https://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb https://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb https://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb https://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse

#wsl1升级到wsl2 “系统找不到指定的文件”,最终发现是wsl1上的子系统没有卸载干净，在Woindows powerShell上注销子系统
#查看有哪些子系统
#wsl --list --all
#注销子系统
#wsl --unregister Ubuntu-22.04
然后重新安装
```

参考链接 

[https://zhuanlan.zhihu.com/p/641118300](https://zhuanlan.zhihu.com/p/641118300)

[https://blog.csdn.net/weixin_42081389/article/details/118501287](https://blog.csdn.net/weixin_42081389/article/details/118501287)

[https://www.zhihu.com/question/492599940/answer/3227538537](https://www.zhihu.com/question/492599940/answer/3227538537)