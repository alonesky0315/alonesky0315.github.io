---
layout: post
title: Target class [xxxxController] does not exist.
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

> Laravel 8配置好路由后，访问提示：“Target class [xxxxController] does not exist.”错误。出现这个错误的原因是Laravel8对路由命名空间做出了更新（详见：路由命名空间更新），而我们仍然在使用Laravel6或者7版本的方式写路由。

Laravel 8路由配置方式：

```
use App\Http\Controllers\UserController;
Route::get('/user', [UserController::class, 'index']);
```

Laravel 6/7路由配置方式：

```
Route::get('logn', 'LoginController@index');
```
在Laravel8中想使用这种配置方式需要在app/Providers/RouteServiceProvider.php文件中，取消$namespace的注释即可使用Laravel 6/7路由配置方式。

```
// protected $namespace = 'App\\Http\\Controllers';//取消对这句代码的注释。
```


原文链接：[https://blog.csdn.net/SunNang/article/details/121795474](https://blog.csdn.net/SunNang/article/details/121795474)