---
layout: post
title: FastAdmin 前端文本内容
category: HTML
keywords: Common HTML
tags: Common HTML
description: 
---

> 主要用于app审核

Index.php控制器
```php
/**
* 服务协议
*/
public function serviceAgreement()
{
    $articleInfo = model('app\api\model\Article')->where('id', 1)->field('id,title,content')->find();
    $this->assign('articleInfo', $articleInfo);
    return $this->view->fetch('agreement');
}
```

agreement.html页面
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{$articleInfo.title}</title>
    <style>
        body{margin: 0 auto;width: 80%;}
        p img{width:100%;height:auto;}
    </style>
</head>
<body>
{$articleInfo.content}
</body>
</html>
```