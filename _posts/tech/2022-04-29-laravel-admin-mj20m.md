---
layout: post
title: Laravel-admin删除预处理
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

```
控制器添加
/**
 * 删除
 * @param $id
 * @return mixed
 */
public function destroy($id)
{
    $count = Db::table('equipment')
        ->where(array('brand' => $id))
        ->whereNull('deleted_at')
        ->count();
    if (!empty($count)) {
        return $this->form()->response()->error('用户有发布的信息资源禁止删除');
    } else {
        if ($this->form()->destroy($id)) {
            return $this->form()->response()->success('删除成功');
        }
    }
}
```

参考链接：[https://learnku.com/articles/5513/laravel-admin-development-notes](https://learnku.com/articles/5513/laravel-admin-development-notes)