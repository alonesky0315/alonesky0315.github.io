---
layout: post
title: ThinkPHP5 where 子查询
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

#### 子查询
```
1)
->where(['user_id'=>15,'by_object'=>19])

sql: `user_id` = 15 AND `by_object` = 19
2)
->whereOr(['user_id'=>15,'by_object'=>19])

sql: `user_id` = 15 OR `by_object` = 19
3)
->where(function($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 AND `by_object` = 19 )
4)
->where(function($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 OR `by_object` = 19 ) 
5)
->whereOr(function($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 AND `by_object` = 19 )
6)
->whereOr(function($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 OR `by_object` = 19 )
7)
->where(['user_id'=>15,'by_object'=>19])
->where(['periods'=>202103])

sql: `user_id` = 15 AND `by_object` = 19 AND `periods` = 202103
8)
->where(['user_id'=>15,'by_object'=>19])
->where(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: `user_id` = 15 AND `by_object` = 19 AND ( `user_id` = 15 AND `by_object` = 19 ) 
9)
->where(['user_id'=>15,'by_object'=>19])
->where(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: `user_id` = 15 AND `by_object` = 19 AND ( `user_id` = 15 OR `by_object` = 19 )
10)
->where(['user_id'=>15,'by_object'=>19])
->whereOr(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: `user_id` = 15 AND `by_object` = 19 OR ( `user_id` = 15 AND `by_object` = 19 )
11)
->where(['user_id'=>15,'by_object'=>19])
->whereOr(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: `user_id` = 15 AND `by_object` = 19 OR ( `user_id` = 15 OR `by_object` = 19 )
12)
->whereOr(['user_id'=>15,'by_object'=>19])
->where(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: `user_id` = 15 OR `by_object` = 19 AND ( `user_id` = 15 AND `by_object` = 19 ) 

13)
->whereOr(['user_id'=>15,'by_object'=>19])
->where(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: `user_id` = 15 OR `by_object` = 19 AND ( `user_id` = 15 OR `by_object` = 19 )
14)
->whereOr(['user_id'=>15,'by_object'=>19])
->whereOr(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: `user_id` = 15 OR `by_object` = 19 OR ( `user_id` = 15 AND `by_object` = 19 )
15)
->whereOr(['user_id'=>15,'by_object'=>19])
->whereOr(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: `user_id` = 15 OR `by_object` = 19 OR ( `user_id` = 15 OR `by_object` = 19 )
16)
->where(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})
->where(['user_id'=>19,'by_object'=>15])

sql: ( `user_id` = 15 AND `by_object` = 19 ) AND `user_id` = 19 AND `by_object` = 15
17)
->where(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})
->where(['user_id'=>19,'by_object'=>15])

sql: ( `user_id` = 15 OR `by_object` = 19 ) AND `user_id` = 19 AND `by_object` = 15
18)
->whereOr(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})
->where(['user_id'=>19,'by_object'=>15])

sql: ( `user_id` = 15 AND `by_object` = 19 ) AND `user_id` = 19 AND `by_object` = 15
19)
->whereOr(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})
->where(['user_id'=>19,'by_object'=>15])

sql: ( `user_id` = 15 AND `by_object` = 19 ) OR `user_id` = 19 OR `by_object` = 15
19)
->where(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})
->where(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 AND `by_object` = 19 ) AND ( `user_id` = 15 AND `by_object` = 19 )
20)
->where(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})
->where(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 AND `by_object` = 19 ) AND ( `user_id` = 15 OR `by_object` = 19 ) 
21)
->where(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})
->whereOr(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 AND `by_object` = 19 ) OR ( `user_id` = 15 AND `by_object` = 19 )
22)
->where(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})
->whereOr(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 AND `by_object` = 19 ) OR ( `user_id` = 15 OR `by_object` = 19 )
23)
->where(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})
->where(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 OR `by_object` = 19 ) AND ( `user_id` = 15 AND `by_object` = 19 ) 
24)
->where(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})
->where(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 OR `by_object` = 19 ) AND ( `user_id` = 15 OR `by_object` = 19 ) 
25)
->where(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})
->whereOr(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 OR `by_object` = 19 ) OR ( `user_id` = 15 AND `by_object` = 19 )
26)
->where(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})
->whereOr(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 OR `by_object` = 19 ) OR ( `user_id` = 15 OR `by_object` = 19 ) 
27)
->whereOr(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})
->where(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 AND `by_object` = 19 ) AND ( `user_id` = 15 AND `by_object` = 19 )
28)
->whereOr(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})
->where(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 AND `by_object` = 19 ) AND ( `user_id` = 15 OR `by_object` = 19 )
29)
->whereOr(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})
->whereOr(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 AND `by_object` = 19 ) OR ( `user_id` = 15 AND `by_object` = 19 )
30)
->whereOr(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})
->whereOr(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 AND `by_object` = 19 ) OR ( `user_id` = 15 OR `by_object` = 19 )
31)
->whereOr(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})
->where(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 OR `by_object` = 19 ) AND ( `user_id` = 15 AND `by_object` = 19 ) 
32)
->whereOr(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})
->where(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 OR `by_object` = 19 ) AND ( `user_id` = 15 OR `by_object` = 19 ) 
33)
->whereOr(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})
->whereOr(function ($query){
    $query->where(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 OR `by_object` = 19 ) OR ( `user_id` = 15 AND `by_object` = 19 )
34)
->whereOr(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})
->whereOr(function ($query){
    $query->whereOr(['user_id'=>15,'by_object'=>19]);
})

sql: ( `user_id` = 15 OR `by_object` = 19 ) OR ( `user_id` = 15 OR `by_object` = 19 )
35)
->where(function($query){
    $query->whereOr(function($query){
        $query->where(['user_id'=>15,'by_object'=>19]);
    });
    $query->whereOr(function($query){
        $query->where(['user_id'=>15,'by_object'=>19]);
    });
})
->where(['periods'=>202012])

sql: ( ( `user_id` = 15 AND `by_object` = 19 ) OR ( `user_id` = 15 AND `by_object` = 19 ) ) AND `periods` = 202012

未完待续

```