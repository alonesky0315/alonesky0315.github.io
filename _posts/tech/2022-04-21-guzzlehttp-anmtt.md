---
layout: post
title: GuzzleHttp简单使用
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

```
$client = new \GuzzleHttp\Client();
$url='http://restapi.amap.com/v3/geocode/regeo';

// GET 请求
$query = [
    'query'=>[
        'param' => $param,
    ]
];
$response = $client->get($url,$query);

// POST 请求
$query = [
    'json' => [
        'storeNo'=>'小熊猫幺儿'
    ],
    'headers' => [
        'Content-Type' => 'application/json',
    ]
];
$response = $client->post($url, $query);

// 参数式请求
$query = [
    'json' => [
        'storeNo'=>'小熊猫幺儿'
    ],
    'headers' => [
        'Content-Type' => 'application/json',
    ]
];
$client->request('POST',$url,query);

$code = $response->getStatusCode();
$result = $response->getBody()->getContents();
```

参考链接：[https://www.jianshu.com/p/5d489f2d39f5](https://www.jianshu.com/p/5d489f2d39f5)