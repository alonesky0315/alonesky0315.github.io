---
layout: post
title: 宝塔安装Zfile
category: Linux
keywords: Common Linux
tags: Common Linux
description: 
---

### 宝塔安装Zfile

> Zfile基于 Java 的在线网盘程序，支持对接 S3、OneDrive、SharePoint、又拍云、本地存储、FTP 等存储源，支持在线浏览图片、播放音视频，文本文件等文件类型。


1. 安装java
```
yum install -y java-1.8.0-openjdk unzip
```

2. 下载java文件到服务器
```
mkdir /wwwroot/Zfile && cd /wwwroot/Zfile
wget https://c.jun6.net/ZFILE/zfile-release.jar
```

3. 宝塔创建java网站配置   
 提示： 如果填写了域名，访问域名即可（记得将域名指向服务），如果使用的是端口，则使用端口访问.
![image.png](https://blog.alonesky.com/storage/article/2023/12/30/m2xYtlZ1dH9Tj1bYfhrZAcpz17aZTpF2EjoSnC3y.png)


4. 配置文件   
如需要修改配置文件, 请下载配置文件 [application.properties](https://file.alonesky.com/file/application.properties) 传到上一步 ZFile 文件所在目录下，命名为 application.properties. 然后在宝塔的 ZFile 配置中（参考上一步）的表单项 项目执行命令 原内容后增加内容：
```
--spring.config.location=file:/www/wwwroot/zfile/application.properties
# 这里的 /www/wwwroot/zfile/application.properties 为你上传到服务器配置文件的路径，注意这行注释不要复制，只复制上面的那一行，注意上移行最前面有一个空格，记得复制（用于和原内容保持一个空格距离）
```

如原来 项目执行命令 为：
```
/usr/bin/java -jar -Xmx1024M -Xms256M/www/wwwroot/zfile/zfile-release.jar --server.port=2825
```

修改后
```
/usr/bin/java -jar -Xmx1024M -Xms256M /www/wwwroot/zfile/zfile-release.jar --server.port=2825 --spring.config.location=file:/www/wwwroot/zfile/application.properties
```

这里的前半部分 /usr/bin/java -jar -Xmx1024M -Xms256M /www/wwwroot/zfile/zfile-release.jar --server.port=2825 也是示例，不表示你宝塔的 ZFile 配置中的表单项项目执行命令原内容。

#### 更新版本

更新步骤如下：

1. 宝塔中停止 ZFile 程序（务必先停止，且尝试访问网页无法访问再继续下面的操作）
2. 重复步骤 下载项目 下载最新版本程序，覆盖原来的 zfile-release.jar 文件（其实就是先删除之前的，再将新版本程序放到同路径同名）
3. 启动项目，访问验证。（一般启动需要 1-3 分钟，访问如果还是之前的版本，请清除浏览器缓存！）

#### 其他设置

宝塔 nginx 默认只支持上传最大 50MB 的文件，可去以下页面进行设置:

![image.png](https://blog.alonesky.com/storage/article/2023/12/30/yNdk4dCikJgantzwc4bZUAnFeksxXlF7rk2Ozku4.png)

![image.png](https://blog.alonesky.com/storage/article/2023/12/30/vQnsxERK5FVtVOrEUB431myKkNt9iLJqEdjL1oCV.png)


#### 自建 OnlyOffice

Docker & Docker Compose 部署

安装Docker并运行
```
yum install docker

docker run --restart=always --name onlyoffice \
    -p 8090:80 \
    -e JWT_ENABLED=false \
    -v /app/onlyoffice/DocumentServer/logs:/var/log/onlyoffice \
    -v /app/onlyoffice/DocumentServer/data:/var/www/onlyoffice/Data \
    -v /app/onlyoffice/DocumentServer/lib:/var/lib/onlyoffice \
    -v /app/onlyoffice/DocumentServer/db:/var/lib/postgresql \
    onlyoffice/documentserver:7.1.1
```
自定义端口和数据目录：

端口号：-p 右侧的 8090 表示对外暴露的端口号，如其他程序占用，则请修改为其他端口号。

数据目录：-v 表示数据文件映射到宿主机目录，如看不懂或不需要，可删除。

Docker Compose 部署
```
mkdir /www/wwwroot/onlyoffice && cd /www/wwwroot/onlyoffice

touch docker-compose.yml
```

docker-compose.yml文件内容
```
version: '3'
services:
  onlyoffice:
    container_name: onlyoffice
    image: onlyoffice/documentserver:7.1.1
    restart: always
    ports:
     - "8090:80"
    environment:
     - JWT_ENABLED=false
    volumes:
     - '$PWD/logs:/var/log/onlyoffice'
     - '$PWD/data:/var/www/onlyoffice/Data'
     - '$PWD/lib:/var/lib/onlyoffice'
     - '$PWD/db:/var/lib/postgresql'
```

自定义端口和数据目录：

端口号：ports 下一行冒号左侧的 8090 表示对外暴露的端口号，如其他程序占用，则请修改为其他端口号。

数据目录：volumes 表示数据文件映射到宿主机目录，如看不懂或不需要，可删除。

#### 反向代理 - WebSocket 支持

创建一个网站配置反向代理

如使用 Nginx 或其他诸如宝塔使用了 Nginx 的工具进行了反向代理，则需要额外增加 Nginx 配置来支持 WebSocket:
```
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

如果使用了 HTTPS，则还需要增加配置：
```
proxy_set_header X-Forwarded-Proto https;
```
![image.png](https://blog.alonesky.com/storage/article/2023/12/30/nttn8Unw1m2RLXdbxoEM0MlJBn8Kjr8tNZX7KFkw.png)

#### 验证部署结果

然后访问：http://你的域名或IP:端口号 即可，如果访问后显示以下样式则表示成功：
![image.png](https://blog.alonesky.com/storage/article/2023/12/30/EziWK0pb2uiiQ1fL34OrfddVelhdj9uPQxbHBfQH.png)

配置到 ZFile 中

![image.png](https://blog.alonesky.com/storage/article/2023/12/30/GpDH3TwjblxCTBPvX2QM3TMOyhZVfKvobXgGnPbN.png)