---
layout: post
title: fastadmin自定义下拉分页和搜索
category: PHP
keywords: Common HTML PHP
tags: Common HTML PHP
description: 
---

#### fastadmin自定义下拉分页和搜索
```php
public function getDealerList()
{
    $where = [];
    $page = $this->request->request("pageNumber");
    $nickname = $this->request->request("nickname");
    $keyValue = $this->request->post("keyValue");
    if (!empty($keyValue)) {
        if (substr_count($keyValue, ',') >= 1) {
            $where['id'] = ['in', $keyValue];
        } else {
            $where['id'] = $keyValue;
        }
    }
    if (!$this->auth->isSuperAdmin() && in_array(3, $this->auth->getGroupIds())) {
        $where['id'] = $this->auth->id;
    }
    if(!empty($nickname)){
        $where['nickname'] = ['like','%'.$nickname.'%'];
    }
    $list = model('Admin')
        ->alias('a')
        ->field('id,nickname')
        ->join('auth_group_access aga', 'a.id=aga.uid', 'left')
        ->where(['aga.group_id' => 3])
        ->where($where)
        ->page($page, 10)
        ->select();
    $total=model('Admin')
        ->alias('a')
        ->field('id,nickname')
        ->join('auth_group_access aga', 'a.id=aga.uid', 'left')
        ->where(['aga.group_id' => 3])
        ->where($where)
        ->count();
    return json(['list' => $list, 'total' => $total]);
}
```
```html
<div class="form-group">
    <label class="control-label col-xs-12 col-sm-2">{:__('Dealer')}:</label>
    <div class="col-xs-12 col-sm-8">
        <input id="c-dealer_id" data-rule="required" data-source="auth/admin/getDealerList" data-field="nickname"
            class="form-control selectpage" name="row[dealer_id]" type="text" value="">
    </div>
</div>
```

参考网址：[https://ask.fastadmin.net/question/16808.html](https://ask.fastadmin.net/question/16808.html)