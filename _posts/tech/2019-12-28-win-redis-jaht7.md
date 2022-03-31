---
layout: post
title: Windows搭建Redis
category: PHP
keywords: 
tags: PHP 工具
description: 
---

### redis版本
副版本号为偶数时，表示是稳定版本，建议在生产环境中使用   
副版本号为基数时，表示是测试版本，不建议在生产环境中是用   
php扩展安装：   
使用phpinfo()函数查看PHP的版本信息，安全模式，下载对应的文件   
![image.png](https://blog.alonesky.com/storage/article/2019/12/28/qnWqXgZCm4iH4hADbpcSo289sOkipti2nu0kildf.png)   
redis中的php扩展下载地址：[https://windows.php.net/downloads/pecl/releases/redis/](https://windows.php.net/downloads/pecl/releases/redis/)   
igbinary扩展下载地址：[https://windows.php.net/downloads/pecl/releases/igbinary/2.0.8/](https://windows.php.net/downloads/pecl/releases/igbinary/2.0.8/)    
解压缩后，将php_redis.dll和php_redis.pdb拷贝至php的ext目录下    
编辑php.ini    
加入这两行代码：     
extension=php_igbinary.dll   
extension=php_redis.dll    
注意：extension=php_igbinary.dll一定要放在extension=php_redis.dll的前面，否则此扩展不会生效    
重启一下web服务器，在phpinfo里面找一下redis扩展：    
![image.png](https://blog.alonesky.com/storage/article/2019/12/28/Ajb9EysExPeUuydSNirKJkjKLMQQxgtjjudTy1js.png)    
这就说明你的redis扩展也安装成功了！！！     

redis中的php扩展下载地址：[https://windows.php.net/downloads/pecl/releases/redis/](https://windows.php.net/downloads/pecl/releases/redis/)    
igbinary扩展下载地址：[https://windows.php.net/downloads/pecl/releases/igbinary/2.0.8/](https://windows.php.net/downloads/pecl/releases/igbinary/2.0.8/)   
redis命令：[http://redisdoc.com/](http://redisdoc.com/)    
参考链接：[https://www.cnblogs.com/lxwphp/p/10597809.html](https://www.cnblogs.com/lxwphp/p/10597809.html)    
附件下载：[https://file.alonesky.com/file/php_7_1_18_nts_RedisDesktopManager.zip](https://file.alonesky.com/file/php_7_1_18_nts_RedisDesktopManager.zip)