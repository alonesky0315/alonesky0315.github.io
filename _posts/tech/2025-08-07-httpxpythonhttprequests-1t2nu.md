---
layout: post
title: HTTPX：Python中的下一代HTTP客户端，比Requests更强大！
category: Python
keywords: Common Python
tags: Common Python
description: 
---

#### HTTPX：Python中的下一代HTTP客户端，比Requests更强大！

> 在Python生态系统中，requests库长期以来一直是HTTP客户端的首选工具。然而，随着异步编程和HTTP/2的普及，开发者们需要一个更现代、更灵活的解决方案。这时候，httpx应运而生！

一、什么是httpx？

 HTTPX 是一个功能强大且现代化的Python HTTP客户端库，支持同步和异步请求，并内置HTTP/2支持。它提供了与requests类似的API，但增加了许多新特性，使其成为新一代HTTP工具的首选。

***主要特性:***
1. 同步 & 异步支持：
    * 支持传统的同步请求，也支持异步请求（基于asyncio或trio）。
    * 异步请求性能强劲，支持HTTP/2。
2. HTTP/2支持：
    * 默认支持HTTP/1.1，可通过可选的httpx[http2]扩展启用HTTP/2。
    * 支持服务器推送（Server Push）等HTTP/2特性。
3. 更快的性能：
    * 对比requests，httpx在连接复用和异步请求方面表现更优。
    * 支持HTTP/2的连接复用，减少了连接建立的开销。
4. 类型注解友好：
    * 完全兼容Python的类型提示（Type Hints），方便静态类型检查。
    * 提供了完善的类型注解，提高了代码的可读性和可维护性。
5. 强大的客户端配置：
    * 支持连接超时、代理、Cookie管理、认证等高级功能。
    * 提供了灵活的配置选项，满足不同场景的需求。
6. 兼容requests API：
    * 如果你熟悉requests

二、安装：
```
pip install httpx
#HTTP/2支持(可选)
pip install httpx[http2]
```
三、基本用法：
1. 同步请求：
	```python
	import httpx

	#连接复用
	with httpx.Client() as client:
		response = client.get("https://httpbin.org/get")
		print(response.status_code)
		print(response.json())

	#单次请求
	response = httpx.get("https://httpbin.org/get")
	print(response.status_code)
	print(response.json())
	```
2. 异步请求：
	```python
	import httpx
	import asyncio

	async def main():
		async with httpx.AsyncClient() as client:
			response = await client.get("https://httpbin.org/get")
			print(response.status_code)
			print(response.json())

	asyncio.run(main())
	```
3. 发送POST请求：
	```python
	import httpx
	
	response = httpx.post("https://httpbin.org/post", json={"key": "value"})
	print(response.status_code)
	print(response.json())
	```
4. 使用httpx[http2]扩展启用HTTP/2：
	```python
	import httpx

	#单次请求
	client = httpx.Client(http2=True)
	response = client.get("https://httpbin.org/get")
	print(response.status_code)
	print(response.http_version)
	print(response.json())

	#连接复用
	with httpx.Client(http2=True) as client:
		response = client.get("https://httpbin.org/get")
		print(response.status_code)
		print(response.http_version)
		print(response.json())
	```
5. 使用代理：
	```python
	import httpx
	#配置代理，通常需要同时指定http和https协议
	proxies = {
		"http://": "http://127.0.0.1:8080",
		"https://": "http://127.0.0.1:8080"  # https请求也使用相同的代理
	}
	client = httpx.Client(proxies=proxies, timeout=10.0)
	response = client.get("https://httpbin.org/get")
	print(response.status_code)
	print(response.json())
	```

四、与Requests对比：

| 特性 | Requests | httpx |
| :---: | :---: | :---: |
| 同步/异步 | 同步 | 同步/异步 |
| HTTP/2 | 不支持 | 支持 |
| 连接复用 | 不支持 | 支持 |
| 类型注解 | 不支持 | 支持 |
| 配置灵活 | 有限 | 丰富 |
| 性能 | 一般 | 好 |

如果你的项目需要高性能、异步支持或HTTP/2，那么httpx无疑是更好的选择！

五、结语

HTTPX 不仅继承了requests的易用性，还引入了许多现代HTTP客户端所需的功能。无论是同步还是异步编程，它都能提供出色的体验。如果你还没尝试过httpx，现在就是最佳时机！

参考链接：[https://mp.weixin.qq.com/s/A3Ug4aqLrqtchOjMDkIfJQ](https://mp.weixin.qq.com/s/A3Ug4aqLrqtchOjMDkIfJQ)