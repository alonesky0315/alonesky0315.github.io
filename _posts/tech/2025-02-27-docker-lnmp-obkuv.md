---
layout: post
title: Docker 搭建 LNMP 环境
category: Linux
keywords: Common Linux
tags: Common Linux
description: 
---

#### Docker 搭建 LNMP 环境

> Docker：是一个开源的应用级别的虚拟化工具，能够让您轻松而优雅地部署多种服务，无需因为开发环境与部署环境的依赖问题而焦头烂耳。
> LNMP：LNMP是指一组通常一起使用来运行动态网站或者服务器的自由软件名称首字母缩写。L指Linux，N指Nginx，M一般指MySQL，也可以指MariaDB，P一般指PHP。

1. 安装 Docker
  ```
  mkdir ~/docker && cd ~/docker && sudo apt-get -y install docker.io
  ```
2. 更换镜像源
  ```
  sudo su -
  cat >> /etc/docker/daemon.json <<- EOF
  {
    "registry-mirrors": ["https://mirror.ccs.tencentyun.com"]
  }
  EOF
  systemctl restart docker
  exit
  ```
3. 下载 Nginx 镜像
  ```
  sudo docker pull nginx:alpine
  ```
4. 下载 PHP 镜像
  ```
  sudo docker pull php:7-fpm-alpine
  ```
5. 下载 PostgreSQL 镜像
  ```
  sudo docker pull postgres:alpine
  ```
6. 启动容器
  ```
  sudo docker run --rm -d -p 80:80 --name nginx nginx:alpine

  这个命令中涉及到的参数有：
  --rm：表示这个容器执行完后会被直接销毁。
  --name：指定这个容器的名称。
  -d：表示这个容器会在后台运行。
  -p：表示开放容器的80端口到主机的80端口。
  -v：表示将nginx的配置文件挂载到容器的对应目录下。
  ```
7. 停止容器
  ```
  sudo docker stop nginx

  // 查看所有容器及其ID
  sudo docker container ls

  // 停止容器
  sudo docker stop <容器ID或容器名称>
  ```
8. 安装 docker-compose
  ```
  // docker-compose 是 Docker 的多个服务部署工具，以方便地同时启动多个容器
  sudo apt-get install -y python-pip && sudo pip install docker-compose
  ```
9.  创建 docker-compose 的配置文件
  ```
  touch /docker/docker-compose.yml
  ```
10.  编辑 docker-compose 的配置文件
  ```
  version: "3"
  services:

    Nginx:
      image: nginx:alpine
      ports:
        - 80:80
      volumes:
        - ./web:/usr/share/nginx/html:ro
        - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro

    PHP:
      image: undefined01/php:7-fpm-alpine
      volumes:
        - ./web:/var/www/html:rw

    Database:
      image: postgres:alpine
      environment:
        POSTGRES_USER: "postgres"
        POSTGRES_PASSWORD: "rootroot"
      volumes:
        - ./data:/var/lib/postgresql/data:rw
  ```
11.  创建 Nginx 的配置文件
  ```
  touch /docker/nginx.conf
  ```
12.  编辑 Nginx 的配置文件
  ```
  server {
      listen       80;
      server_name  localhost;

      location / {
          root   /usr/share/nginx/html;
          index  index.php index.html index.htm;
      }

      error_page   500 502 503 504  /50x.html;
      location = /50x.html {
          root   /usr/share/nginx/html;
      }

      location ~ \.php$ {
          fastcgi_pass   PHP:9000;
          fastcgi_index  index.php;
          fastcgi_param  SCRIPT_FILENAME  /var/www/html/$fastcgi_script_name;
          include        fastcgi_params;
      }
  }
  ```
13.  使用 docker-compose 启动服务
  ```
  sudo docker-compose up -d

  // 查看启动的服务
  sudo docker container ls
  ```
14.  编辑权限
  ```
  sudo chmod -R 777 ./data ./web
  ```
15.  创建index.php文件
  ```
  touch /docker/web/index.php
  ```
16.  编辑index.php文件
  ```
  <?php
  phpinfo();
  ?>
  ```
17.  创建test.php文件
  ```
  touch /docker/web/test.php
  ```
18.  编辑test.php文件
  ```
  <?php
  $dbconn = pg_connect('host=Database user=postgres password=rootroot') or die('Could not connect: ' . pg_last_error());
  pg_query('CREATE TABLE IF NOT EXISTS test ( tester INT )');

  pg_query('INSERT INTO test VALUES (0)');
  $res = pg_query('SELECT * FROM test') or die('Query failed: ' . pg_last_error());
  $num = pg_num_rows($res);
  echo "You have visited this site $num times";

  pg_free_result($res);
  pg_close($dbconn);
  ?>
  ```
19.  使用 docker-compose 停止服务
  ```
  sudo docker-compose down
  ```

常用命令：

```
// 查看 docker 目录下的所有文件和目录（包括隐藏文件和目录）
ls  -la /docker

// 以长格式列出data 目录下的所有文件（包括隐藏文件），并且不会对文件列表进行排序
ls -lf ./data
```

  
原文链接：[https://cloud.tencent.com/lab/courseDetail/682679941464569](https://cloud.tencent.com/lab/courseDetail/682679941464569)