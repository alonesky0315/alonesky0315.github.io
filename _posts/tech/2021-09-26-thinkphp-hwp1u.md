---
layout: post
title: ThinkPHP语法
category: PHP
keywords: 
tags: PHP 常识
description: 
---

1. 判断文件是否含有.mp4
```
<eq name="vo:thumb|geturl|strstr='.mp4'" value=".mp4">
</eq>
```
2. 覆盖append
```
model中 添加了append后
在控制器循环中覆盖append
$item['department']->append(['user','avatar_text'],true);
在控制器循环中隐藏字段
$item['department']->hidden(['user','avatar_text']);
```
3. thinkphp  条件使用
```
thinkphp 5.0.18
>  查询
1)
$where['finished']=['exp',Db::raw('>= `personal`')];
$model->where($where)->find();
2)
$model->whereExp('finished','>= `personal`')->find();
3)
$where['']=['exp',Db::raw("FIND_IN_SET(".$finished.",finished)")];
4) 
$where['finished'] = array('exp',"concat(`finished`,'1')");
5)
$where[] = array("concat(',',`ids`,',')",'like','%,2,%'); //未验证 
> > 更新
1)
$data['finished']=Db::raw('`personal`+1');
$model->where('id',1)->update($data);
2)
$model->where('id',1)->exp('finished','`personal`+1')->update();
> 关联
$model
    ->join('user', 'FIND_IN_SET(user.user_id,b.user_id)')
    ->field("b.*,user.nickName,user.phone")
    ->group('b.bank_id')
    ->select();
```
thinkphp EXP使用:[https://blog.alonesky.com/tp50exp-wdfqb](https://blog.alonesky.com/tp50exp-wdfqb)

4. 按指定数组顺序排序
```php
$userIds='';
foreach ($teamList as $teamListItem) {
    $userIds.=$teamListItem['team_ids'].',';
}
$userIds=rtrim($userIds,',');
$where['user_id']=['in',$userIds];
$exp = new \think\db\Expression('field(user_id,'.$userIds.')');
$userList=$userModel
    ->where($where)
    ->field('user_id,username,nickName,avatarUrl,residue_integral,residue_integral_fixed')
    ->order($exp)
    ->select();
sql：SELECT 字段 FROM yoshop_user WHERE isleader = 1  AND user_id IN (254,252) ORDER BY field(user_id,254,252)
```
5. 上一条下一条
```
$lastId=$this->where(['cid'=>$detail['cid'],'id'=>['lt',$article_id]])->max('id');
$nextId=$this->where(['cid'=>$detail['cid'],'id'=>['gt',$article_id]])->min('id');
$lastArticle=$this
	->field('author,is_recommend,reading', true)
	->where(['id'=>$lastId])
	->find();
// echo $this->getLastSql();exit;
$nextArticle=$this
	->field('author,is_recommend,reading', true)
	->where(['id'=>$nextId])
	->find();
```
持续更新中......