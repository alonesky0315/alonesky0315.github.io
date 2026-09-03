---
layout: post
title: 基于 CentOS 搭建 Django 环境
category: Python
keywords: Common Python
tags: Common Python
description: 
---

#### 基于 CentOS 搭建 Django 环境

1. 安装 setuptools 工具
    ```
    yum install python-setuptools -y
    ```
2. 下载Django
    ```
    wget https://www.djangoproject.com/m/releases/1.11/Django-1.11.3.tar.gz --no-check-certificate
    ```
3. 解压 Django
    ```
    tar -zxvf Django-1.11.3.tar.gz
    ```
4. 修改文件夹名称
    ```
    mv Django-1.11.3 Django
    ```
5. 安装 Django
    ```
    cd Django && python setup.py install
    ```
6. 新建 HelloWorld 项目
   ```
   python /usr/lib/python2.7/site-packages/Django-1.11.3-py2.7.egg/django/bin/django-admin.py startproject HelloWorld
   ```
7. 修改配置文件./Django/HelloWorld/HelloWorld/settings.py   
    将 `ALLOWED_HOSTS = []` 修改为 `ALLOWED_HOSTS = ["ip"]` ，这样可以允许通过 IP 访问 ，在实际运营中一般要改为对应的域名
8. 数据库迁移
    ```
    python manage.py migrate
    ```
9. 启动项目
  ```
  cd HelloWorld && python manage.py runserver 0.0.0.0:80
  ```