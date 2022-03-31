---
layout: post
title: Linux服务器扩容分区和文件系统(阿里云服务器)
category: Linux
keywords: 
tags: 常识 Linux
description: 
---

#### linux服务器扩容分区和文件系统_Linux系统盘
> 为防止操作失误导致数据丢失，建议您操作前使用快照备份数据   
##### 根据操作系统安装growpart或者xfsprogs扩容格式化工具   
- Alibaba Cloud Linux 2、CentOS 7   
```
yum install cloud-utils-growpart
yum install xfsprogs
```
- Ubuntu 14、Ubuntu 16、Ubuntu 18、Debian 9   
```
apt install cloud-guest-utils
apt install xfsprogs
```
- Debian 8、OpenSUSE 42.3、OpenSUSE 13.1、SUSE Linux Enterprise Server 12 SP2  
请使用上游版本（upstream）的growpart或者xfsprogs工具   
##### 运行uname -a命令查看实例的内核版本。
如果内核版本大于等于3.6.0，请参见[高内核版本的操作步骤](https://help.aliyun.com/document_detail/111738.html?spm=5176.11346930configuration.0.dexternal.2c605ca37VCjg5#section-gxq-3tw-dhb)。       
如果内核版本小于3.6.0，如CentOS 6、Debian 7和SUSE Linux Enterprise Server 11 SP4等发行版，需要经过一次控制台重启或者API重启才能完成分区扩容。请参见[低内核版本的操作步骤](https://help.aliyun.com/document_detail/111738.html?spm=5176.11346930configuration.0.dexternal.2c605ca37VCjg5#section-vxq-3tw-dhb)       
##### 扩展高内核版本实例的系统盘分区和文件系统
1、运行`fdisk -l`命令查看现有云盘大小   
![image.png](https://blog.alonesky.com/storage/article/2020/09/24/SFqd4M5p4MHSH9f3TdjWiN2AJU8SwORhc4dQi3AR.png)   
2、运行df -Th命令查看云盘分区大小和文件系统类型   
![image.png](https://blog.alonesky.com/storage/article/2020/09/24/pHP9UEtbiviRhJMB1T85jF4bBCzk8Xd4vqfrVhA9.png)   
3、运行growpart <DeviceName> <PartionNumber>命令扩容分区。   
示例：`growpart /dev/vda 1`  //扩容系统盘的第一个分区（/dev/vda1）   
![image.png](https://blog.alonesky.com/storage/article/2020/09/24/EtL4QQvZeabeCI5MzakNeSET0vHmq2zzzGP7u4Fg.png)  
若运行命令后出现以下错误，您可以运行`LANG=en_US.UTF-8`命令切换ECS实例的字符编码类型    
![image.png](https://blog.alonesky.com/storage/article/2020/09/24/upYANS5NAyEZaTkFGEUBreazbNe6XfcdbDXrwwGQ.png)   
运行命令切换字符编码后，建议您使用`reboot`命令重启ECS实例。   
4、扩展文件系统   
ext*文件系统（例如ext3和ext4）：运行resize2fs <PartitionName>命令。     
示例：`resize2fs /dev/vda1` //扩容系统盘的/dev/vda1分区的文件系统    
![image.png](https://blog.alonesky.com/storage/article/2020/09/24/yxx2oyfgjyupcJ2vXrqiA4E8eEypXEIigsAryXBv.png)   
xfs文件系统：运行xfs_growfs <mountpoint>命令   
示例：`xfs_growfs /` //扩容系统盘的/dev/vda1分区的文件系统。其中根目录（/）为/dev/vda1的挂载点    
![image.png](https://blog.alonesky.com/storage/article/2020/09/24/OpURruBpn5fJhIq5XgGHUC3hH4jxHEPcZcEOl8UQ.png)    
5、运行`df -h`命令查看云盘分区大小。   
![image.png](https://blog.alonesky.com/storage/article/2020/09/24/pEp4mQ2ihlXrH1bJHgedUcTryz1ahuQrvdy9Xk3M.png)

参考链接：
[https://help.aliyun.com/document_detail/113316.htm](https://help.aliyun.com/document_detail/113316.htm)