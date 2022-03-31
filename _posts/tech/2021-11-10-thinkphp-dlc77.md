---
layout: post
title: ThinkPHP常用函数
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

```
/**
 * 获取当前域名及根路径
 * @return string
 */
function base_url()
{
    static $baseUrl = '';
    if (empty($baseUrl)) {
        $request = Request::instance();
        $subDir = str_replace('\\', '/', dirname($request->server('PHP_SELF')));
        $baseUrl = $request->scheme() . '://' . $request->host() . $subDir . ($subDir === '/' ? '' : '/');
        // $baseUrl = 'https://' . $request->host() . $subDir . ($subDir === '/' ? '' : '/');
    }
    return $baseUrl;
}

/**
 * 遍历输出所有参数并die
 */
function die_dump()
{
    $vars = func_get_args();
    foreach ($vars as $var) {
        dump($var);
    }
    die();
}

/**
 * 遍历输出所有参数并die
 */
function die_dump()
{
    $vars = func_get_args();
    foreach ($vars as $var) {
        dump($var);
    }
    die();
}

```