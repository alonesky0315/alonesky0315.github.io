---
layout: post
title: ThinkPHP5默认首页
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

#### index/controller/Index.php
```php 
namespace app\index\controller;
class Index {
    public function index(){
        $html='
            <style type="text/css">
                *{ padding: 0; margin: 0; } 
                div{ padding: 4px 48px;} 
                a{color:#2E5CD5;cursor: pointer;text-decoration: none} 
                a:hover{text-decoration:underline; } 
                body{ background: #fff; font-family: "Century Gothic","Microsoft yahei"; color: #333;font-size:18px;} 
                h1{ font-size: 100px; font-weight: normal; margin-bottom: 12px; } 
                p{ line-height: 1.6em; font-size: 42px }
                .position{position: absolute;bottom: 10px;left: 40%;}
            </style>
            <div style="padding: 24px 48px;"> 
                <h1>:)</h1>
                <p> It is working<br/></p>
            </div>
            <div class="position"><a href="https://beian.miit.gov.cn" target="_blank">鲁ICP备2021000000号-1</a></div>
        ';
    	return  $html;
    }
}
```
#### index/config.php
```php
//配置文件
return [
    // 默认输出类型
    'default_return_type' => 'html',
    // 默认全局过滤方法 用逗号分隔多个
    'default_filter' => 'trim,htmlspecialchars',
    // 模板设置
    'template' => [
        // 模板引擎类型 支持 php think 支持扩展
        'type' => 'think',
        // 模板路径
        'view_path' => '',
        // 模板后缀
        'view_suffix' => 'php',
        // 模板文件名分隔符
        'view_depr' => DS,
        // 模板引擎普通标签开始标记
        'tpl_begin' => '{{',
        // 模板引擎普通标签结束标记
        'tpl_end' => '}}',
        // 标签库标签开始标记
        'taglib_begin' => '{{',
        // 标签库标签结束标记
        'taglib_end' => '}}',
    ],
];
```