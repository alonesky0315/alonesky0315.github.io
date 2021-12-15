---
layout: post
title: 水平方随机文章、关联关键词
category: 技术
keywords: 水平方,随机文章,关联关键词
tags: PHP shuipf
description: 水平方随机文章、关联关键词
---
### 水平方随机文章、关联关键词

### 一、关键词文章
1.修改文件：/shuipf/Application/Content/TagLib/Content.class.php

在 ```$where['status'] = array("EQ", 99);```下面添加(在283行附近)
```
if(!empty($catid)){
    if(!empty(getCategory($catid,'child'))){
        $arrchildid=getCategory($catid,'arrchildid');
        $cat_array=explode(',', $arrchildid);
        $where['catid'] = array('IN',$cat_array);
    }else{
        $where['catid'] = $catid;
    }
}
```
在 ```$number += count($r);```下面修改foreach（在378行附近）
```
foreach ($r as $id => $v) {
    if($data['nid']!=$v['id']){
        $key = $v['catid'].'_'.$v['id'];
        if ($i <= $data['num'] && !in_array($id, $key_array)) {
            $key_array[$key] = $v;
        }
    }
    $i++;
} 
```               
2.调用
```
<content action="relation" relation="$relation" catid="分类id" 
order="id DESC" num="10" keywords="$keywords" nid="$id">
	<volist name="data" id="vo">
		<li><a href='{$vo.url}' target="_blank">{$vo.title}</a></li>
	</volist>
</content>
```
### 二、随机文章
1.修改文件：/shuipf/Application/Content/TagLib/Content.class.php

添加函数 rands（）一般末尾还有一个大括号
```
/**
 * @Author   JunjunChen
 * @DateTime 2019-03-21T11:11:13+0800
 * @method rands
 * @param action string,catid int (非必须),num int ;
 * @version  [version]
 * @param    [type]                   $data [description]
 * @return   [array]                         [description]
 */
public function rands($data){
    $catid = intval($data['catid']);
    $where=array();
    $child=getCategory($catid,'child');
    if(!empty($child)){
        $arrchildid=getCategory($catid,'arrchildid');
        $cat_array=explode(',', $arrchildid);
        $where['catid'] = array('IN',$cat_array);
    }else{
        $where['catid'] = $catid;
    }
    $dataList = M("Arcticle")
    ->where($where)
    ->limit($data['num'])
    ->order('rand()')->select();
    //echo M()->getlastsql();exit;
    return $dataList;
}
```
2.调用
```
<content action="rands" catid="$catid" num="7" >
<volist name="data" id="vo">
<li><a href='{$vo.url}' target="_blank">{$vo.title}</a></li>
</volist>
</content>
```
