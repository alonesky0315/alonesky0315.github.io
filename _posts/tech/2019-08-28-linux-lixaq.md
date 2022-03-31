---
layout: post
title: Linux服务器挂载第二块磁盘
category: Linux
keywords: 
tags: Linux 常识
description: 
---

### linux服务器挂载第二块磁盘
Linux磁盘挂载是比较常见的管理操作之一。   
	预装的linux系统有2块盘，一块为系统盘，另外一块磁盘是数据盘，默认没有挂载，需要手动挂载到系统中。具体操作是：需要对磁盘进行格式化，格式化后挂载到需要的挂载点，最后还需要添加分区启动表，以便下次系统启动随机自动挂载。
详细操作步骤为：   
			1、首先查看系统中磁盘信息，命令为：`fdisk -l`输入后显示的："Disk /dev/vda: 21.5 GB",即为系统盘，名称为vda，另外还有一块磁盘是没有格式化，没有分区，没有在使用中的："Disk /dev/vdb: 32.2 GB"，如图1所示:   
![image.png](https://blog.alonesky.com/storage/article/2019/08/28/1YW7NJPsx9xmhwKNWUrIRqFzoxhpaqbke9au1Zkt.png)      
    2、将未使用的磁盘进行格式化，操作数据盘符前，请自行确认磁盘是否有使用过，如有重要数据请谨慎操作，以免导致数据丢失，带来不必要的麻烦。具体格式化命令为： `mkfs.ext3 /dev/vdb (或 mkfs –t ext3 /dev/vdb)`如图2所示，即为正在格式化中。这个时候请耐心等待格式化完毕。          
![image.png](https://blog.alonesky.com/storage/article/2019/08/28/12vTRe0gLgZtvbigYixOckFMEvEjRsCZ4K2NDb6r.png)    
    3、将格式化完的磁盘进行挂载，挂载前，先在服务器上创建一个需要挂载的挂载点，如可以在根目录下创建一个wwwroot目录。创建目录命令为： `mkdir /wwwroot`    
      挂载磁盘到wwwroot目录，挂载命令： `mount /dev/vdb /wwwroot/`    
    4、修改fstab，以便系统启动时自动挂载磁盘，编辑fstab默认启动文件命令： `vi /etc/fstab` 回车在其中添加一行： `/dev/vdb /wwwroot ext3 defaults 0 0` 如图3所示，在fstab中添加的一行,添加后，保存。    
![image.png](https://blog.alonesky.com/storage/article/2019/08/28/gQIVcepsSs7vLQ4Rne5eUQUnrCgSmMOivFxPFEG3.png)    
      然后如图4所示，输入：`sync` 将缓存写入服务器，并执行：`reboot` 进行优雅重启服务器。   
 ![image.png](https://blog.alonesky.com/storage/article/2019/08/28/DSfU6Yj8nSmOZSrfQvPgjFlcEmDhNcBTtTzbDIlm.png)    
    5、重启服务器后，输入命令： `df -lh` 查看第2块磁盘是否有正常挂载，正常情况您会看到如图5所示，这样第2块盘就挂载好了。  
![image.png](https://blog.alonesky.com/storage/article/2019/08/28/ux5qlxZ1wMFWLugMuhriZqhMzlJLoKwnSGOB9f7o.png)   
ext3文件类型解析：[https://blog.csdn.net/weixin_39212776/article/details/81016007](https://blog.csdn.net/weixin_39212776/article/details/81016007)   
原文：[https://www.cnblogs.com/cqwo/p/7920730.html](https://www.cnblogs.com/cqwo/p/7920730.html)