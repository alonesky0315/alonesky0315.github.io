---
layout: post
title: 常见几个方式禁止IP访问网站(PHP、Nginx、Apache不同设置方法)
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

> 由于运维工作的需要，我们需要禁止指定的IP或者IP段访问网站，于是我们可以用常见的Nginx设置，但是我们其实还可以用PHP脚本设置直接加载在我们的访问页面头部。或者我们也有可以用到Apache环境脚本如何设置的，在这里老蒋整理几个常见的设置方法。

1. PHP禁止IP和IP段访问
    ```
    <?
    //禁止某个IP
    $banned_ip = array(
        "127.0.0.1",
        //"119.6.20.66",
        "192.168.1.4"
    );
    if (in_array(getenv("REMOTE_ADDR"), $banned_ip)) {
        die("您的IP禁止访问！");
    }
    //禁止某个IP段
    $ban_range_low = ip2long("119.6.20.65");
    $ban_range_up = ip2long("119.6.20.67");
    $ip = ip2long($_SERVER["REMOTE_ADDR"]);
    if ($ip > $ban_range_low && $ip < $ban_range_up) {
        echo "您的IP在被禁止的IP段之中，禁止访问！";
        exit();
    }
    ```

2. Apache 禁止IP访问方法

    Linux下 规则文件.htaccess(手工创建.htaccess文件到站点根目录)

    ```
    <IfModule mod_rewrite.c>
    RewriteEngine On
    #Block ip
    RewriteCond %{http:X-Forwarded-For}&%{REMOTE_ADDR}&%{http:X-Real-IP} (8.8.4.4|8.8.8.) [NC]
    RewriteRule (.*) - [F]
    </IfModule>
    ```

    windows2003下 

    ```
    #Block ip
    RewriteCond %{HTTP_X_FORWARDED_FOR}&%{REMOTE_ADDR}&%{HTTP_X-Real-IP} (8.8.4.4|8.8.8.) [NC]
    RewriteRule (.*) - [F]
    ```

    windows2008下 规则文件web.config (手工创建web.config文件到站点根目录)

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="band ip" stopProcessing="true">
                        <match url="(.*)" />
                        <conditions logicalGrouping="MatchAny">
                            <add input="%{HTTP_X_FORWARDED_FOR}&amp;%{REMOTE_ADDR}&amp;%{HTTP_X_Real_IP}" pattern="(8.8.4.4|8.8.8.)" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>  
    </configuration>
    ```

参考链接：

[https://www.lnmpweb.cn/archives/281](https://www.lnmpweb.cn/archives/281)

[https://www.itbulu.com/setting-ip-blocked.html](https://www.itbulu.com/setting-ip-blocked.html)