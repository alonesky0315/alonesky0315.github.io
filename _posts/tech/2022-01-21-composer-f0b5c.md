---
layout: post
title: Composer 常识
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

1.将Composer版本升级到最新
```
composer self-update --preview
```
2.清除缓存
```
composer clearcache
```
3.全局使用阿里云Composer镜像
```
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```
4.取消配置
```
composer config -g --unset repos.packagist
```
5.仅当前项目使用该镜像地址
```
composer config repo.packagist composer https://mirrors.aliyun.com/composer/
```
6.取消当前工程镜像配置
```
composer config --unset repos.packagist
```
7.输出详细的信息
```
composer -vvv require alibabacloud/sdk
```
8.执行诊断
```
composer diagnose
```
9.更新 composer.lock 文件
```
composer update --lock
```
10.安装依赖包
```
composer install
```
11.更新依赖包(可多个)
```
# 更新所有依赖包
composer update
# 更新指定的包
composer update monolog/monolog
# 更新指定的多个包
composer update monolog/monolog symfony/dependency-injection
# 还可以通过通配符匹配包
composer update monolog/monolog symfony/*
```
12.安装依赖包
```
composer require monolog/monolog
# 安装指定范围版本
composer require monolog/monolog ~1.2.3
```
13.移除依赖包(可多个)
```
composer remove monolog/monolog
```
14.搜索依赖包
```
# 输出包及其描述信息
composer search monolog
# 输出包及其描述信息
composer search --only-name monolog
```
15.列出当前项目使用的依赖包
```
# 列出所有已经安装的包
composer show
# 可以通过通配符进行筛选
composer show monolog/*
# 显示具体某个包的信息
composer show monolog/monolog
```

参考链接：[https://www.runoob.com/w3cnote/composer-install-and-usage.html](https://www.runoob.com/w3cnote/composer-install-and-usage.html)