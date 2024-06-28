---
layout: post
title: ThinkPHP+redis 消息队列
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

> 在 ThinkPHP 中，队列（Queue）是一个非常重要的组件，用于处理后台耗时任务、发送邮件、推送消息等异步操作。通过使用队列，你可以将需要较长时间执行的任务放到后台去处理，从而避免阻塞用户的请求。

1. 安装 topthink/think-queue 扩展包
```
composer require topthink/think-queue
```
2. 创建队列任务类
```php
namespace app\common\model;
use think\queue\Job;
class QueueJob
{
    public function push(Job $job, $data)
    {
        // 执行逻辑  
        // 如果任务执行成功，删除任务  
        $job->delete();  
        // 如果任务执行失败，也可以重试一定次数  
        // $job->release($delay); // $delay为延迟时间
    }
    public function failed($data)
    {
        // 任务执行失败时的处理逻辑
        
    }
}
```

3. 推送任务到队列
```php
use think\Queue;
// 立即推送
Queue::push('app\common\model\QueueJob@push', $data, 'queueJob');
// 延迟30分钟推送
Queue::later(30*60, 'app\common\model\QueueJob@push', $data, 'queueJob');
```

4. 监听和处理队列任务
```
php think queue:listen --queue queueJob
```

5. 配置队列配置
```php
// application\extra\queue.php
return [
    'connector'  => 'Redis',
    'expire'     => null,   // 任务过期时间，默认为60秒，若要禁用，则设置为 null
    'default'    => 'push_decoration',  // 默认的队列名
    'host'       => '127.0.0.1',   // redis 主机ip
    'port'       => 6379,   // redis 端口
    'password'   => '',   // redis 密码
    'select'     => 3,   // 使用哪里一个 db，默认为 db0
    'timeout'    => 0,   // redis 连接的超时时间
    'persistent' => false,
];
```