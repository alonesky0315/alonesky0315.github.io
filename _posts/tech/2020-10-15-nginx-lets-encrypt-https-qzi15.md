---
layout: post
title: 解决证书错误： 服务器缺少中间证书
category: 知识库
keywords: 
tags: 常识
description: 
---

#### 解决 Nginx Let's Encrypt HTTPS 证书 错误： 服务器缺少中间证书

>起初是由于在ios系统出现https连接ssl握手时间过长
> 经过调查有网友说是ssl中间证书缺失 
> 时间长 和 中间证书缺失 这两点是否存在关联目前还有待考证
> 不过目前发现
> Nginx 配 Let's Encrypt 证书的确存在中间证书缺失问题 
> 本文介绍如何解决这一问题

这里证书用的是 Let's Encrypt 通配符证书 

服务端 Nginx 1.16.0

首先检查 是否存在这一问题

检测地址: 
[https://www.myssl.cn/tools/check-server-cert.html](https://www.myssl.cn/tools/check-server-cert.html)

检测结果: 
![image.png](https://blog.alonesky.com/storage/article/2020/10/15/MIIcfxgvhOObHbqwLLMYZeKA5tDio21htQBLK1xP.png)

Nginx相关配置:
```
server {
    listen    443  ssl http2 default_server;
    server_name    zzzmh.cn;
    ssl_certificate    /root/ssl/fullchain.pem;
    ssl_certificate_key    /root/ssl/privkey.pem;
    ssl_trusted_certificate /root/ssl/chain.pem;

    ...
}
```
关键问题是在 fullchain.pem 

根据调查 Nginx 不像是 Apache 有专门的参数配置中间证书 

Nginx 需要全部配置在 fullchain.pem 中

顾正确的格式是分为三段 

分别代表 服务器层 中间层 root层
```
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
```
而实际上一般 Let's Encrypt 生成的证书 

fullchain.pem 默认都是两段
```
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
```

解决:
其实具体缺失中间证书是不是一个问题，有什么后果，都尚未可知。 

只是抱着能解决就解决的心态去调查解决的。

首先先找到  

Let's Encrypt 生成出证书的4个文件

cert.pem

chain.pem

fullchain.pem

privkey.pem

将 cert.pem 内容复制到这个地址解析 
[https://www.myssl.cn/tools/downloadchain.html](https://www.myssl.cn/tools/downloadchain.html)

解析成功可以获得2个下载按钮 

使用 [点击下载中间证书文件]

将下载到的文件中的 内容 复制到 fullchain.pem 的 两段内容的中间 ，保存即可。

(需要注意 fullchain.pem 格式要求极为严格 不能存在空行 空格等)

最后重启nginx 问题解决

> 测试配置是否正常
```
nginx -t
```
> 重新加载配置文件
```
nginx -s reload
```
再次去到一开始检测的网址 

检测结果: 
![image.png](https://blog.alonesky.com/storage/article/2020/10/15/n54pwOkemECh2xLKkQZYlp8TEunxUzCaKX5cZfo0.png)
原文链接：[https://zzzmh.cn/single?id=100](https://zzzmh.cn/single?id=100)