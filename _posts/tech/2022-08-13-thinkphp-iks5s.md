---
layout: post
title: ThinkPHP中获取指定日期后工作日的具体日期方法
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

1. 安装扩展
```
composer require nyg/holiday
```
2. 使用
```
function getWorkDate($start_time=0,$days=15){
    // write_log('$start_time'.$start_time);
    $end_time=($start_time+$days*86400)-1;
    // write_log('$end_time'.$end_time);
    $Holiday=new Holiday();
    // $Holiday->update($start_time);
    $holidays_day = $Holiday->count($start_time,$end_time);
    // write_log('$holidays_day'.$holidays_day);
    if(empty($holidays_day)){
        return $end_time;
    }else{
        $start_time=$end_time+1;
        return $this->getWorkDate($start_time,$holidays_day);
    }
}
```
参考网址：[https://github.com/nyg123/holiday](https://github.com/nyg123/holiday)