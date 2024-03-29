---
layout: post
title: Fastadmin【Thinkphp5.0】安装使用Workerman实现websocket
category: JavaScript
keywords: Common JavaScript PHP
tags: Common JavaScript PHP
description: 
---

> Workerman是一款纯PHP开发的开源高性能的PHP socket 服务器框架。被广泛的用于手机app、手游服务端、网络游戏服务器、聊天室服务器、硬件通讯服务器、智能家居、车联网、物联网等领域的开发。 支持TCP长连接，支持Websocket、HTTP等协议，支持自定义协议。基于workerman开发者可以更专注于业务逻辑开发，不必再为PHP Socket底层开发而烦恼。
 
1. 安装workerman
```
composer require topthink/think-worker
```
如果需要在window下做服务端，还需要
```
composer require workerman/workerman-for-win
```

运行出现错误PHP Fatal error: Call to undefined function Workerman\Lib\pcntl_signal()，需要删除vendor\workerman\workerman，防止命名覆盖

2. 在项目根目录创建server.php文件
```server.php
#!/usr/bin/env php
<?php
define('APP_PATH', __DIR__ . '/application/');
define('BIND_MODULE','push/Worker');
// 加载框架引导文件
require __DIR__ . '/thinkphp/start.php';
```
3. 在application\push\controller目录创建Worker.php文件
```Worker.php
<?php
namespace app\push\controller;
use think\worker\Server;
class Worker extends Server
{
    protected $socket = 'websocket://127.0.0.1:7272';

    /**
     * 收到信息
     * @param $connection
     * @param $data
     */
    public function onMessage($connection, $data)
    {
        $connection->send($data);
    }

    /**
     * 当连接建立时触发的回调函数
     * @param $connection
     */
    public function onConnect($connection)
    {
        $connection->send('与前端建立连接成功');
    }

    /**
     * 当连接断开时触发的回调函数
     * @param $connection
     */
    public function onClose($connection)
    {
        $connection->send('与前端断开连接成功');
    }

    /**
     * 当客户端的连接上发生错误时触发
     * @param $connection
     * @param $code
     * @param $msg
     */
    public function onError($connection, $code, $msg)
    {
        echo "error $code $msg\n";
    }

    /**
     * 每个进程启动
     * @param $worker
     */
    public function onWorkerStart($worker)
    {

    }
}
```
4. 命令行下运行，启动监听服务
```
php server.php start
```
5. 在app\index\view\index目录创建index.php文件 
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Workerman</title>
    <link rel="shortcut icon" href="/favicon.ico"/>
</head>
<body>
</body>
<script type="text/javascript">
    // ws模式
    ws = new WebSocket("ws://域名:7272");
    // wss模式(需要配置Nginx代理)
    // ws = new WebSocket("wss://域名/websocket");
    ws.onopen = function() {
        console.log("与服务端连接成功");
        ws.send('tom');
        console.log("发送给服务端的消息：tom");
    };
    ws.onmessage = function(e) {
        console.log("收到服务端的消息：" + e.data);
    };
</script>
</html>
```
5.1 wss模式配置Nginx代理(ws模式可省略此步骤)
```
location /websocket {
		proxy_pass http://127.0.0.1:7272;
		proxy_http_version 1.1;
		proxy_set_header Upgrade $http_upgrade;
		proxy_set_header Connection "upgrade";
		proxy_read_timeout 180s;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header REMOTE-HOST $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		client_max_body_size 5000M;
}
```
6. 浏览器访问，查看控制台输出

### 备注：
#### ws与wss区别

WebSocket协议（WS）和WebSocket Secure协议（WSS）是WebSocket的两种不同模式，主要区别在于安全性。以下是具体分析：   
安全性：WSS在TLS之上实现WebSocket通信，通过加密连接提供数据传输的安全，而WS则不加密传输。   
端口号：通常，WS使用80端口，而WSS使用443端口。   
证书要求：WSS需要SSL证书来验证域名，保证连接的安全性，而WS不需要证书。   
总的来说，WSS与ws就和HTTP与HTTPS的关系类似，WSS相当于WebSocket的加密版本。在对数据传输安全性有要求的场景下，推荐使用WSS以保护数据不被窃取或监听。而在一些对安全性要求不高或者内部网络环境中，可以考虑使用WS以简化配置和提高兼容性。

参考链接：[https://blog.csdn.net/netuser1937/article/details/134758166](https://blog.csdn.net/netuser1937/article/details/134758166)