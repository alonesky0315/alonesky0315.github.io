---
layout: post
title: DcatAdmin 更换wangEditor编辑器
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

####  DcatAdmin 更换wangEditor编辑器
1. 安装
```
composer require death_satan/dcat-wang-editor -vvv
```
2. 发布页面
```
php artisan vendor:publish --tag=dcat-wang-editor
```
3. 修改上传目录(vendor\death_satan\dcat-wang-editor\src\Form\WangEditor.php)
```
protected string $imgUploadDirectory = 'uploads/images';
protected string $videoUploadDirectory = 'uploads/videos';
```
4. 使用
```
$form->wangEditor('content')
```

> 上传到COS

1. 配置COS(config\filesystems.php)
```
'disks' => [
	'cos' => [
		'driver' => 'cos',
		'app_id'     => env('COS_APP_ID',''),
		'secret_id'  => env('COS_SECRET_ID',''),
		'secret_key' => env('COS_SECRET_KEY',''),
		'region'     => env('COS_REGION', 'ap-shanghai'),
		'bucket'     => env('COS_BUCKET',''),
		// 可选，如果 bucket 为私有访问请打开此项
		'signed_url' => false,
		// 可选，是否使用 https，默认 false
		'use_https' => true,
		// 可选，自定义域名
		'domain' => '',
		// 可选，使用 CDN 域名时指定生成的 URL host
		'cdn' => env('COS_CDN'),
		'prefix' => env('COS_PATH_PREFIX'), // 全局路径前缀
		'guzzle' => [
				'timeout' => env('COS_TIMEOUT', 60),
				'connect_timeout' => env('COS_CONNECT_TIMEOUT', 60),
		],
	],
]
```
2. 使用
```
$form->wangEditor('content')->disk('cos');
```