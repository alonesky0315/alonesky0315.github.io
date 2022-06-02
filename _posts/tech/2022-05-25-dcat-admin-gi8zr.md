---
layout: post
title: Dcat Admin知识库
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

```
// 安装数据库备份
composer require ghost/dcat-backup
php artisan vendor:publish --provider="Spatie\Backup\BackupServiceProvider"
```

插件地址：[https://gitee.com/dcat-phper/dcat-backup](https://gitee.com/dcat-phper/dcat-backup)

```
// 安装日志
composer require dcat-admin/operation-log
```

插件地址：[https://packagist.org/packages/dcat-admin/operation-log](https://packagist.org/packages/dcat-admin/operation-log)

laravel 排序

```
// 后台指定顺序排序
$grid->model()->orderByRaw(DB::raw('FIELD(use_in, 1,0,2) asc'));

// 指定顺序排序
DB::name('user')->orderByRaw(DB::raw('FIELD(use_in, 1,0,2) asc'));
```

参考链接：[https://blog.csdn.net/zl_j_c/article/details/110217108](https://blog.csdn.net/zl_j_c/article/details/110217108)