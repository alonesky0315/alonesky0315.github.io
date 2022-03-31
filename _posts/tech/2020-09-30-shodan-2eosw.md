---
layout: post
title: 网络空间搜索引擎Shodan
category: 知识库
keywords: 
tags: 常识 工具
description: 
---

#### 网络空间搜索引擎Shodan 
* 黑五(11月第四个星期五) Shodan Membership 只要5刀 
* ElasticSearch的默认端口是9200，在Shodan中输入”port:9200”来进行搜索 
* “product”  对某一产品进行搜索  
* “country”  搜索某个国家的资产  
* 多个词同时搜索时，每个关键字间加空格分割就行，
* 当不需要某个词时，可以用”-”加上关键词来进行去除，比如不想要Amazon的ElasticSearch服务器，就可以”-org:amazon”   

##### 常用的搜索语句

|  关键词 | 描述 | 示例 |
|:------:|:-----:|:----:|
|  hostname  |  搜索指定的主机或域名  |  hostname:"google"  |
|  asn  |  搜索区域自治编号  |  无  |
|  port |  搜索指定的端口或服务  |  port:"9200"  |
|  org  |  搜索指定的组织或公司  |  org:"amazon"/org:"google"  |
|  os  |  搜索指定的操作系统类型  |  os:"win"  |
|  http.html  |  搜索指定的网页内容  |  http.html:"Supervisor Status"  |
|  html.title  |  搜索指定的网页标题  |  html.title:"Supervisor Status"  |
|  http.server  |  搜索指定的http请求返回中server的类型  |  无  |
|  http.status  |  搜索指定的http请求返回响应码的状态  |  http.status:"200"  |
|  country  |  搜索指定的国家  |  country:"CN"  |
|  city  |  搜索指定的城市  |  city:"Hefei"  |
|  product  |  搜索指定的操作系统/软件/平台  |  product:"Apache httpd"  |
|  vuln  |  搜索指定的CVE漏洞编号  |  vuln:"CVE-2014-0723"  |
|  net  |  搜索指定的IP地址或子网  |  net:"210.45.240.0/24"  |
|  isp  |  搜索指定的ISP供应商  |  isp:"China Telecom"  |
|  version  |  搜索指定的软件版本  |  version:"1.6.2"  |
|  geo  |  搜索指定的地理位置，参数为经纬度  |  geo:"31.8639, 117.2808"  |
|  before/after  |  搜索指定收录时间前后的数据，格式为dd-mm-yy  |  before:"11-11-15"  |

##### 搜索实例
查找位于合肥的 Apache 服务器：    
`apache city:"Hefei" `     
查找位于国内的 Nginx 服务器：    
`nginx country:"CN"`         
查找 GWS(Google Web Server) 服务器：           
`"Server: gws" hostname:"google"`           
查找指定网段的华为设备：           
`huawei net:"61.191.146.0/24"`            

![image.png](https://blog.alonesky.com/storage/article/2020/09/30/YbRM2HoKEItmQtI1eURi9BrUrlNtlECgy30zmXZH.png)

　参考网址：   
  [https://www.cnblogs.com/H4ck3R-XiX/p/12950736.html](https://www.cnblogs.com/H4ck3R-XiX/p/12950736.html)   
 [https://www.cnblogs.com/miaodaren/p/7904484.html](https://www.cnblogs.com/miaodaren/p/7904484.html)    
 [https://www.freebuf.com/sectool/121339.html](https://www.freebuf.com/sectool/121339.html)