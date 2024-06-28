---
layout: post
title: ThinkPHP软删除
category: PHP
keywords: PHP
tags: PHP
description: 
---

ThinkPHP软删除

> ThinkPHP>=5.0.2	deleteTime 属性改为非静态定义

在实际项目中，对数据频繁使用删除操作会导致性能问题，软删除的作用就是把数据加上删除标记，而不是真正的删除，同时也便于需要的时候进行数据的恢复。

要使用软删除功能，需要引入SoftDelete trait，例如User模型按照下面的定义就可以使用软删除功能：

```
namespace app\index\model;

use think\Model;
use traits\model\SoftDelete;

class User extends Model
{
    use SoftDelete;
    protected $deleteTime = 'delete_time';
}
```

5.0.2版本之前deleteTime属性必须使用static定义。

deleteTime属性用于定义你的软删除标记字段，ThinkPHP5的软删除功能使用时间戳类型（数据表默认值为Null），用于记录数据的删除时间。

可以用类型转换指定软删除字段的类型，建议数据表的所有时间字段统一一种类型。

定义好模型后，我们就可以使用：
```
// 软删除
User::destroy(1);
// 真实删除
User::destroy(1,true);
$user = User::get(1);
// 软删除
$user->delete();
// 真实删除
$user->delete(true);
默认情况下查询的数据不包含软删除数据，如果需要包含软删除的数据，可以使用下面的方式查询：

User::withTrashed()->find();
User::withTrashed()->select();
如果仅仅需要查询软删除的数据，可以使用：

User::onlyTrashed()->find();
User::onlyTrashed()->select();
如果你的模型定义了base基础查询，请确保添加软删除的基础查询条件。
```

文档地址：[https://www.kancloud.cn/manual/thinkphp5/189658](https://www.kancloud.cn/manual/thinkphp5/189658)