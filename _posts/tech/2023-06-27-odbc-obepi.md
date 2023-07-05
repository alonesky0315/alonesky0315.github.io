---
layout: post
title: ODBC驱动器管理器
category: Tool
keywords: Tool
tags: Tool
description: 
---

> 在使用Navicat Premium 连接 sql server 数据库的时候报错 “【ODBC驱动器管理器】未发现数据源名称并且未指定默认驱动程序”

一、进入控制面板管理工具模块

Win+R 输入control进入控制面板>系统和安全>管理工具>ODBC数据源

![image.png](https://blog.alonesky.com/storage/article/2023/06/27/DqiBgwtm73M88M6zq6i8tCLFZNkG2uEW2kARjvxs.png)

> 注：64位指的是电脑是64位的

二、添加SQL Server驱动程序

双击ODBC数据源，添加SQL Server驱动程序

![image.png](https://blog.alonesky.com/storage/article/2023/06/27/chWdLtzUMwTC6BM6tAIuzn6mpt8bGkLmd11GfnAN.png)

三、输入数据库配置

![image.png](https://blog.alonesky.com/storage/article/2023/06/27/geTBua1TKBICcWDo7EEzClv9TIMftTdV4lVOFbFu.png)

四、输入验证方式

![image.png](https://blog.alonesky.com/storage/article/2023/06/27/mURn5qujRHC8u7UY3GRo8h3TQSd4nzm1LLitm5e4.png)

五、选择需要连接的数据库

![image.png](https://blog.alonesky.com/storage/article/2023/06/27/4wAhhiqHfKxDkOdkpJqxlQqTxshq5CGMtJxBdURr.png)

六、打开Navicat Premium，连接>SQl Server，测试连接成功后，确定即可。（注意：ip与端口号之间是英文逗号）

![image.png](https://blog.alonesky.com/storage/article/2023/06/27/QaaoQEdGvMam0MMh6OZECQd8EYY9U8smUwjx0rpr.png)

> 如果配置还是不成功，再来最后一步
> 安装navicat自带sqlncli_x64.msi，就在安装目录下，安装后问题解决！
> 在软件安装目录里可以找到

![image.png](https://blog.alonesky.com/storage/article/2023/06/27/cFGU9DrP8WgJr9IFQFgPHOHFyXxNue7pZkzlpfcy.png)

参考链接：[https://blog.csdn.net/weixin_38115942/article/details/123585168](https://blog.csdn.net/weixin_38115942/article/details/123585168)