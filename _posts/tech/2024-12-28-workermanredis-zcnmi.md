---
layout: post
title: Workerman+Redis实现消息通知
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

> 利用Workerman+Redis实现消息通知

#### 前端
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>消息通知</title>
    <style type="text/css">
        .notice-lay{
            position: fixed;
            right: 20px;
            top: 50px;
            z-index: 222;
            display: flex;
            flex-direction: column;
        }
        .notice-box2,
        .notice-box{
            background: rgba(223,223, 223, 0.5);
            border-radius: 10px;
            -webkit-border-radius: 10px;
            -moz-border-radius: 10px;
            -ms-border-radius: 10px;
            -o-border-radius: 10px;
            box-shadow: 2px 2px 2px #bdbdbd;
            z-index: 999;
            padding: 10px 20px;
          transition: all .3s;
          -webkit-transition: all .3s;
          -moz-transition: all .3s;
          -ms-transition: all .3s;
          -o-transition: all .3s;
           font-size:14px;
          color:#333;
          line-height: 1.5;
            opacity: 0;
            height:0px;
            overflow: hidden;
            margin-top:10px;
            transform: translateX(200%);
            -webkit-transform: translateX(200%);
            -moz-transform: translateX(200%);
            -ms-transform: translateX(200%);
            -o-transform: translateX(200%);
        }
        .notice-box2 a,
        .notice-box a{
            color:#095f8a;
        }
        .notice-box2 b,
        .notice-box b{
          color:red;
          padding:0 4px;
        }
        .notice-box2.on,
        .notice-box.on{
          opacity: 1;
          height: auto;
          transform: translateX(0%);
            -webkit-transform: translateX(0%);
            -moz-transform: translateX(0%);
            -ms-transform: translateX(0%);
            -o-transform: translateX(0%);
        }
    </style>
</head>
<body>
    <div class="notice-lay">
        <div class="notice-box">
            前台<b id="number"></b>位新用户注册，等待审核中！
            <a id="open_user" href="javascript:void(0);">点击查看</a>
        </div>
        <div class="notice-box2">
            前台<b id="number2"></b>笔新订单，等待审核凭证！
            <a id="open_order" href="javascript:void(0);">点击查看</a>
        </div>
    </div>
    <audio src="assets/common/user.mp3" id="myAudio" style="display:none;" controls></audio>
    <audio src="assets/common/order.mp3" id="myAudio2" style="display:none;" controls></audio>
</body>
<script type="text/javascript">
    $('#open_order').click(function () {
        axios({
            method: "post",
            url: '/index.php?s=/store/index/order',
            headers: {
                'Content-Type': 'multipart/form-data'
            },
            data: []
        }).then(function (res) {
            window.location.href = 'index.php?s=/store/order/pay_list';
        })

    });
    $('#open_user').click(function () {
        axios({
            method: "post",
            url: '/index.php?s=/store/index/regular',
            headers: {
                'Content-Type': 'multipart/form-data'
            },
            data: []
        }).then(function (res) {
            window.location.href = '/index.php?s=/store/user/index&process=2&label_id=0&keywords=';
        })
    });

    $(function () {
        ws = new WebSocket("wss://domain.com/wss");
        ws.onopen = function () {
            console.log("连接成功");
        };
        ws.onmessage = function (e) {
            console.log("收到服务端的消息：" + e.data);
            var count = JSON.parse(e.data);
            if (count) {
                if (count.register != '0') {
                    // 判断是否相同，不相同打开提示声音
                    var number = $('#number').html();
                    if (count.register != number) {
                        playSound();
                    }
                    $('#number').text(count.register);
                    $('.notice-box').addClass('on');
                } else {
                    $('#number').text('0');
                    $('.notice-box').removeClass('on');
                }
                if (count.order != '0') {
                    // 判断是否相同，不相同打开提示声音
                    var number2 = $('#number2').html();
                    if (count.order != number2) {
                        playSound2();
                    }
                    $('#number2').text(count.order);
                    $('.notice-box2').addClass('on');

                } else {
                    $('#number2').text('0');
                    $('.notice-box2').removeClass('on');
                }
            } else {
                $('#number').text('0');
                $('.notice-box').removeClass('on');
            }
        };
    });
    function playSound() {
        var audio = document.getElementById('myAudio');
        if (audio) {
            audio.play();
        } else {
            // 如果未使用 HTML5 <audio> 元素，则使用 Audio 对象
            var sound = new Audio('assets/common/user.mp3');
            sound.play();
        }
    }
    function playSound2() {
        var audio = document.getElementById('myAudio2');
        if (audio) {
            audio.play();
        } else {
            // 如果未使用 HTML5 <audio> 元素，则使用 Audio 对象
            var sound = new Audio('assets/common/order.mp3');
            sound.play();
        }
    }
</script>
</html>
```
#### 服务端
##### 反向代理
```shell
 location /wss
  {
    proxy_pass http://127.0.0.1:2346;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
    proxy_set_header X-Real-IP $remote_addr;
  }
```


##### 服务端路由
```php
#!/usr/bin/env php
// 定义运行目录
define('WEB_PATH', __DIR__ . '/');
// 定义应用目录
define('APP_PATH', WEB_PATH . '../source/application/');
define('BIND_MODULE','store/Worker');
// 加载框架引导文件
require APP_PATH . '../thinkphp/start.php';
```

##### 服务端控制器
```php
namespace app\store\controller;
use think\worker\Server;
use Workerman\Lib\Timer;
class Worker extends Server{
    protected $socket = 'websocket://127.0.0.1:2346';
    public function onMessage($connection, $data)
    {
        $connection->send('提示：'.$data);
    }
    /**
     * 当连接建立时触发的回调函数
     * @param $connection
     */
    public function onConnect($connection)
    {
    }
    /**
     * 当连接断开时触发的回调函数
     * @param $connection
     */
    public function onClose($connection)
    {
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
        
       Timer::add(2, function()use($worker){
            $redis = new \Redis();
            $redis->connect('127.0.0.1', 6379);
            $redis->auth('password');
            $register = $redis->sMembers('register');
            $order = $redis->sMembers('haopai_order');
            $count = count($register);
            $count2 = count($order);
            if($count || $count2){
                foreach($worker->connections as $connection) {
                    $connection->send(json_encode([
                        'register' => $count,
                        'order' => $count2
                    ]));
                }
            }else{
                foreach($worker->connections as $connection) {
                    $connection->send(json_encode([
                        'register' => 0,
                        'order' => 0
                    ]));
                }
            }
        });
    }
}

// 订单记录添加到redis
$redis = new \Redis();
$redis->connect('127.0.0.1', 6379);
$redis->auth('password');
$redis->sRem('order',$model['order_no']);

// 用户记录添加到redis
$redis = new \Redis();
$redis->connect('127.0.0.1', 6379);
$redis->auth('password');
// 删除集合
$redis->sRem('register',$model['phone']);
```