---
layout: post
title: 宝塔webhook+coding配置自动部署
category: 知识库
keywords: 
tags: 常识 插件 工具
description: 
---

1. 安装webhook

     宝塔面板搜索webhook，点击安装

![image.png](https://blog.alonesky.com/storage/article/2022/04/08/bN576bluo56iDNRJAvdkAiAHkZ62vcfMXeo7fWSX.png)

2. 添加脚本

    安装完成--点击设置--添加，名称自起，脚本如下代码（复制修改即可），注意：需要改两个地方，一个是项目路径gitPath，一个是git网址gitHttp(如码云的是git@gitee.com)。设置完成点提交即可。

![image.png](https://blog.alonesky.com/storage/article/2022/04/08/jqh0CZhcymAltq9ujIZ6ue8cjSPzSAY2Oh7sGjpk.png)

```
# 执行脚本
# !/bin/bash
echo ""
# 输出当前时间
date --date='0 days ago' "+%Y-%m-%d %H:%M:%S"
echo "-------开始-------"
# 判断宝塔WebHook参数是否存在
# if [ ! -n "$1" ];
# then
# echo "param参数错误"
# echo "End"
# exit
# fi
# 服务器 git 项目路径
gitPath="/www/wwwroot/$1"
# 项目 git 网址
gitHttp="git@e.coding.net:project/$1.git"
echo "路径：$gitPath"
# 判断项目路径是否存在
if [ -d "$gitPath" ]; then
  cd $gitPath
  # 判断是否存在git目录
  if [ ! -d ".git" ]; then
    echo "在该目录下克隆 git"
    git clone $gitHttp gittemp
    mv gittemp/.git .
    rm -rf gittemp
  fi
  # 拉取最新的项目文件
  git reset --hard origin/master
  # git clean -f
  git pull origin master
  echo "拉取完成"
  # 执行npm
  # 执行编译
  # npm run build
  # 设置目录权限
  chown -R www:www $gitPath
  echo "-------结束--------"
  exit
else
  echo "该项目路径不存在"
  echo "End"
  exit
fi
```

3. 码云、github、阿里云云效等webhook配置；点击查看密钥，即可看到

```
http://ip:端口/hook?access_key=access_key&param=aaa
@param access_key string HOOK密钥
@param param string 自定义参数（在hook脚本中使用$1接收）
```

复制链接到设置webhook码云、github、阿里云效的地方，此处的aaa更改为你服务器项目的名称即可。

![image.png](https://blog.alonesky.com/storage/article/2022/04/08/ybebn5h4rPHAqD2GuJxnxQIaMSdCy8Cl1D7glxnP.png)

4. 配置coding

    (1)、coding 新增webhook，监听push请求
		
![image.png](https://blog.alonesky.com/storage/article/2022/04/08/OQVN16Eaqh2qFg6yC5aIgZNDKqBnwAGI2szu0dYr.png)
![image.png](https://blog.alonesky.com/storage/article/2022/04/08/OtXQwjR94jNZ1IPt9HXfGxdp4eqsn1mpGGBn8wjy.png)
![image.png](https://blog.alonesky.com/storage/article/2022/04/08/3oyNBO36J5sNExdNgpvgJjtqPeJqltVUE5BKM47Q.png)
![image.png](https://blog.alonesky.com/storage/article/2022/04/08/UZbYsKjDA4eXhnraGJ6W6i6IDNQPNSoqnUM2cQRM.png)

参考网址：

[https://blog.csdn.net/qq_38997379/article/details/120435949](https://blog.csdn.net/qq_38997379/article/details/120435949)

[https://www.cnblogs.com/aigeileshei/p/13324212.html](https://www.cnblogs.com/aigeileshei/p/13324212.html)