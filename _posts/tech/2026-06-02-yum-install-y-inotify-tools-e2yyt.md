---
layout: post
title: yum install -y inotify-tools 监控网站目录
category: Linux
keywords: Common Linux
tags: Common Linux
description: 
---

#### yum install -y inotify-tools 监控网站目录
**`yum install -y inotify-tools` 包含的命令完全可以监控整个目录，甚至包括该目录下所有子目录及未来新创建的文件。**

在 `inotify-tools` 工具包中，我们主要使用 **`inotifywait`** 这个命令来实现目录监控。

要监控**整个网站目录（包含所有级数的子目录和文件）**，你需要使用 **`-r`（递归）** 参数。

---

### 1. 监控整个目录的终极命令

请在服务器终端执行以下命令（记得把路径换成你网站的实际绝对路径）：

```bash
inotifywait -rm /www/wwwroot/ -e modify -e create -e move -e delete --timefmt '%Y-%m-%d %H:%M:%S' --format '%T %e %w%f'
```

#### 💡 参数详细拆解（为什么能监控整个目录）：

* **`-r` (Recursive)：核心参数。** 告诉系统进行**递归监控**。不仅监控当前目录，还会自动监控它下面的所有子目录、孙子目录。
* **`-m` (Monitor)：** 持续监控。不加这个参数的话，抓到一次修改命令就会自动退出了；加了它，就会像监控摄像头一样一直挂在后台。
* **`-w` (Write)：** 如果目标是一个目录，自动监控该目录下的所有写操作和访问。
* **`-e modify,create,move,delete`：** 指定只监听这几种**恶性篡改行为**（修改、新建、移动/重命名、删除）。这样能过滤掉大量的无用读取（Read）日志。
* **`--timefmt` 和 `--format`：** 格式化输出。让屏幕上打印出来的信息带上精确的年月日时分秒，并且清晰显示是哪个文件被动了。

---

### 2. 运行后的实际效果（捕获黑客现场）

这个命令执行后，终端屏幕会保持静止。一旦有人（或黑客的自动化脚本）对该目录下任何文件进行了操作，屏幕上会**瞬间实时打印**：

* **如果黑客修改了根目录下的 `index.php`：**
> `2026-06-02 11:45:12 MODIFY /www/wwwroot/site/index.php`

* **如果黑客在 `uploads` 子目录下新建了一个潜伏木马 `1.php`：**
> `2026-06-02 11:45:15 CREATE /www/wwwroot/site/uploads/1.php`
> 
> `2026-06-02 11:45:15 MODIFY /www/wwwroot/site/uploads/1.php`

---

### 3. 抓到线索后，怎么“按图索骥”对齐 Nginx 日志？

当你把监控挂在那里，突然看到屏幕上弹出一行：

> `2026-06-02 11:45:12 MODIFY /www/wwwroot/site.com/index.php`

**破案的关键来了：**
黑客能改这个文件，说明他在这**一秒钟**向服务器发送了请求。立刻去翻你的 `site.com.log`（访问日志），寻找 **`02/Jun/2026:11:45:12`** 这一秒的所有请求。

你会发现类似下面的记录：

```nginx
ip - - [02/Jun/2026:11:45:12 +0800] "GET /index.php?g=Wap&a=shows&id=123 HTTP/1.1" 200 ...
```

或者是调用了某个特定的子目录文件。**由于时间精确到了秒，这一秒内产生的几条访问记录里，一定包含着黑客用来触发漏洞的真正 URL 路径！**

---

### ⚠️ 注意事项（避坑指南）

1. **静态缓存导致的“日志刷屏”：**
如果你的网站本身有大量的用户在上传图片，或者系统在自动生成运行缓存（比如 ThinkPHP 的 `Runtime/` 目录），那么这个命令由于监控了整个目录，屏幕上会疯狂滚动 Runtime 缓存文件被修改的提示，干扰你抓黑客。

 **解决方法：** 如果刷屏太厉害，建议缩窄防线，**只死死监控几个最核心的入口文件和配置目录**：
```bash
inotifywait -rmw /www/wwwroot/site/index.php /www/wwwroot/site/config.php -e modify
```

2. **后台运行：**
如果你把 SSH 窗口关了，这个前台监控进程就会断开。如果你想让它在服务器后台不间断运行并把结果写进一个文件里，可以这样执行：
```bash
nohup inotifywait -rmw /www/wwwroot/site.com/ -e modify -e create --timefmt '%Y-%m-%d %H:%M:%S' --format '%T %e %w%f' > /www/wwwlogs/hacker_watch.log 2>&1 &
```

这样它就会在后台静默运行，你只需要过段时间去查看 `/www/wwwlogs/hacker_watch.log` 这个新日志文件，就能看到有没有黑客偷袭的记录了。