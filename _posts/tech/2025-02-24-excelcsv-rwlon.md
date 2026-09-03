---
layout: post
title: 打印函数使用
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

#### 打印函数使用
基本语法
```php
mixed print_r ( mixed $expression [, bool $return = false ] )
```
参数说明：

$expression：必需参数，代表要输出的变量，可以是数组、对象、字符串等各种类型的变量。

$return：可选参数，为布尔类型。当该参数设置为 true 时，print_r() 函数不会直接输出变量信息，而是将结果作为字符串返回；若省略该参数或者设置为 false，函数会直接将变量信息输出到浏览器或终端

```php
/**
 * 打印调试函数
 * @param $content
 * @param $is_die
 */
function pre($content, $is_die = true)
{
    header('Content-type: text/html; charset=utf-8');
    echo '<pre>' . print_r($content, true);
    $is_die && die();
}
```