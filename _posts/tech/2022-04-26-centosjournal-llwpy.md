---
layout: post
title: CentOS系统journal日志清理
category: Linux
keywords: 
tags: 常识 Linux
description: 
---

> CentOS系统中有两个日志服务，分别是传统的 rsyslog 和 systemd-journal
> 
> systemd-journald是一个改进型日志管理服务，可以收集来自内核、系统早期启动阶段的日志、系统守护进程在启动和运行中的标准输出和错误信息，还有syslog的日志。
> 
> 该日志服务仅仅把日志集中保存在单一结构的日志文件/run/log中，由于日志是经历过压缩和格式化的二进制数据，所以在查看和定位的时候很迅速。
> 
> 默认情况下并不会持久化保存日志，只会保留一个月的日志。另外，一些rsyslog无法收集的日志也会被journal记录到。
> 
> rsyslog作为传统的系统日志服务，把所有收集到的日志都记录到/var/log/目录下的各个日志文件中。

常见的日志文件如下：

/var/log/messages 绝大多数的系统日志都记录到该文件

/var/log/secure 所有跟安全和认证授权等日志都会记录到此文件

/var/log/maillog 邮件服务的日志

/var/log/cron crond 计划任务的日志

/var/log/boot.log 系统启动的相关日志

查询日志文件大小

```
ls -lhm --full-time /var/log/journal | sort -k6 | head -n30
```

查询var目录大于100M的文件夹

```
du -t 100M /var
```

查询journal日志大小

```
journalctl --disk-usage
```

设置journal日志大小10M

```
journalctl --vacuum-size=10M
```

查看/var目录的文件大小并排序（单位为MB）

```
du -hm --max-depth=1 /var/ | sort -n
```

清空 /var/log/journal 文件

1. 用echo命令，将空字符串内容重定向到指定文件中(手动执行)

	```
	echo "" > system.journal
	```

2. journalctl 命令自动维护文件大小

	```
	1) 只保留近一周的日志(d天w周m月y年)

	journalctl --vacuum-time=1w

	2) 只保留500MB的日志

	journalctl --vacuum-size=500M

	3) 直接删除 /var/log/journal/ 目录下的日志文件

	rm -rf /var/log/journal/

	```

查看 /var/log/journal/ 日志目录如下：

```
# ll /var/log/journal/
drwxr-sr-x  2 root systemd-journal  4096 Jan 22 11:26 f9d400c5e1e8c3a8209e990d887d4ac1
drwxr-sr-x+ 2 root systemd-journal 12288 Jan 14 15:37 f9d400c5e1e8c3a8209e990d887d4ac1_bk_20190122

然后，再执行 journalctl 限制日志的命令：

# journalctl --vacuum-time=1w
Vacuuming done, freed 0B of archived journals on disk.
 
# journalctl --vacuum-size=500M
Vacuuming done, freed 0B of archived journals on disk.

```

备注：

> 执行 journalctl 命令时报错：Error was encountered while opening journal files: Input/output error
> 
> 问题分析：日志文件损坏
> 
> 解决方法：删除之前的日志，并重启 journalctl 服务
> 
> mv journal/f9d400c5e journal/f9d400c5e_190122_bak
> 
> systemctl restart systemd-journald.service

原文链接：https://blog.csdn.net/qq_42427109/article/details/107881734