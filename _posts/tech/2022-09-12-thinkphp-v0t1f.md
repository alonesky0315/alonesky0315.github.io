---
layout: post
title: ThinkPHP下载文件
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

ThinkPHP下载文件

```
/**
 * 下载文件
 */
public function download()
{
    $filename = $this->request->request('filename');
    $config = get_addon_config('database');
    $backupDir = ROOT_PATH . 'public' . DS . $config['backupDir'];
    $file=$backupDir.$filename;
    if (!file_exists($file)) {
        $this->error("文件不存在");
    }
    //告诉浏览器以附件处理
    header('Content-Disposition: attachment;filename=' . $filename);
    readfile($file);
}
```