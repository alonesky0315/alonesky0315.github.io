---
layout: post
title: Docker 快速入门
category: Linux
keywords: Common Linux
tags: Common Linux
description: 
---

#### Docker 快速入门

1. 卸载旧版本Docker
  ```
  // 列出系统中已安装的Docker包
  yum list installed | grep docker

  // 卸载已安装的Docker包
  yum -y remove docker-ce-cli.x86_64
  yum -y remove docker-ce.x86_64
  yum -y remove containerd.io
  ```
2. 安装相关依赖
  ```
  // 安装 Docker 所需的依赖
  yum install -y yum-utils device-mapper-persistent-data lvm2

  // 添加 Docker 的 yum 源
  yum-config-manager --add-repo https://mirrors.cloud.tencent.com/docker-ce/linux/centos/docker-ce.repo
  ```
3. 安装Docker
  ```
  // yum 安装 Docker
  yum install -y docker-ce docker-ce-cli containerd.io

  // 验证 Docker 版本以确认安装成功
  docker version
  ```

4. 启动Docker
  ```
  // 启动 Docker
  systemctl start docker

  // 将 Docker 设置为开机启动
  systemctl enable docker
  // 
  查看 Docker 运行状态
  service docker status
  ```
5. 配置镜像加速
  ```
  // 创建 Docker 配置目录
  mkdir -p /etc/docker

  // 配置 Docker 镜像加速源
  tee /etc/docker/daemon.json <<-'EOF'
  {
    "registry-mirrors": ["https://mirror.ccs.tencentyun.com"]
  }
  EOF

  // 重启守护进程并重启 Docker
  systemctl daemon-reload && systemctl restart docker
  ```
6. 运行第一个容器
  ```
  docker run --name=hello hello-world

  // 查看容器的进程
  docker ps -a
  ```
7. 拉取镜像
  ```
  docker pull johngong/calibre-web
  // 查看现有的镜像
  docker images
  ```
8. 创建容器
  ```
  docker create --name=calibre-web -p 80:8083 -v /data/calibre-web/library:/library -e WEBLANGUAGE=zh_CN johngong/calibre-web

  docker create 是创建容器的命令
  --name=calibre-web 表示创建的容器的名称
  -p 80:8083 表示该容器将 80 端口映射到 8083 端口
  -v /data/calibre-web/librery:/libray 表示该容器将 /data/calibre-web/library 目录映射为 /library 目录
  -e WEBLANGUAGE=zh_CN 表示该容器定义了一个变量，变量名是 WEBLANGUAGE，变量值是 zh_CN
  johngong/calibre-web 是容器的镜像，这里也就是我们前面拉取的镜像
  ```
9. 查看容器
  ```
  docker ps -a
  // 运行中的容器进程
  docker ps
  ```
10. 启动容器
  ```
  docker start calibre-web
  ```
11. 停止容器
  ```
  docker stop calibre-web

  // 强制停止容器
  docker kill calibre-web
  ```
12. 删除 Docker 容器
  ```
  docker rm hello
  ```
13. 删除运行状态的容器
  ```
  // 启动容器
  docker start calibre-web

  // 删除该容器
  docker rm calibre-web
  ```
14. 删除指定镜像
  ```
  docker rmi hello-world

  // 查看现有镜像
  docker images
  ```
15. 删除所有镜像
  ```
  // 获取所有镜像 ID
  docker images -q

  // 一次性删除所有镜像
  docker rmi `docker images -q`
  ```
	
	原文链接：[https://cloud.tencent.com/lab/courseDetail/768138035069433](https://cloud.tencent.com/lab/courseDetail/768138035069433)