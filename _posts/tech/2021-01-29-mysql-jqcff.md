---
layout: post
title: MySQL常用实例
category: MySQL
keywords: 
tags: 常识 MySQL
description: 
---

```
-- 更新指定订单的发货状态和发货时间
-- update bz_order set `status`=2,send_time=unix_timestamp(now()) where id in('1','5')
-- 查询规格名含有空格的数据
-- select * from bz_specs where `name`!=TRIM(`name`)
-- 查询规格在另一张表中不存在的
-- select  old.* from bz_specs_old as old where not exists
(select 1 from bz_specs as new where old.id=new.id);
-- 从其他表查询数据到主表
-- update fa_user as fu left join (select id,department_id from fa_team) as ft 
on fu.main_department=ft.department_id set fu.main_team = ft.id
-- 更新分销用户 姓名和手机号 如果用户表昵称为空则设置手机号  
-- update yoshop_dealer_user as du left join 
( select user_id, phone, real_name from yoshop_user ) as u on du.user_id = u.user_id 
set du.real_name = case when u.real_name is not null then u.real_name 
else u.phone end ,du.mobile = u.phone
// mysql 正则查询 结果为1表示true,结果为0表示false
// 查询是否包含汉字
-- SELECT specs,hex(specs) FROM bz_goods_specs WHERE hex(specs) REGEXP('e[4-9][0-9a-f]')
// 查询是否包含数字
-- SELECT sepcs from goods_specs where sepcs REGEXP '[0-9.]';
// 查询是否包含字母(不区分大小写)
-- SELECT sepcs from goods_specs where sepcs REGEXP '[a-z]';
// 查询是否包含字母(区分大小写)
-- SELECT sepcs from goods_specs where sepcs REGEXP BINARY '[a-z]';
// 查询某一字段的数据为数字
-- SELECT id,specs FROM `bz_goods_specs` WHERE (specs REGEXP('[^0-9.]'))=1
// 查询三级区域下级数量为0的pid
-- SELECT a.pid, count( CASE WHEN pid = ( SELECT b.id FROM bz_area AS b WHERE b.`level` = 3 AND b.id = a.pid ) THEN 1 END ) AS pid_count FROM bz_area AS a GROUP BY a.pid HAVING pid_count < 1
```