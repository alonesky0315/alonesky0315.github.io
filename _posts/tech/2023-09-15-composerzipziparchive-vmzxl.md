---
layout: post
title: 宝塔运行composer的时候缺失zip（ZipArchive）支持
category: Linux
keywords: Linux PHP
tags: Linux PHP
description: 
---

在SSH命令行界面执行以下语句（php7.4的）：
```
cd /www/server/php/74/src/ext/zip/
/www/server/php/74/bin/phpize
./configure --with-php-config=/www/server/php/74/bin/php-config
make && make install
// 有的走的配置文件是/www/server/php/74/etc/php-cli.ini
echo "extension = zip.so" >> /www/server/php/74/etc/php.ini
```

原文链接：https://blog.csdn.net/lyh3147/article/details/132804178