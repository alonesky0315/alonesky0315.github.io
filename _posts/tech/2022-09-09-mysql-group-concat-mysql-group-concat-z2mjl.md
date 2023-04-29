---
layout: post
title: mysql group concat 去重_MySQL group_concat() 函数用法
category: MySQL
keywords: 
tags: 常识 MySQL
description: 
---

MySQL group_concat() 函数用法

在使用 group by对数据进行分组后，如果需要对 select 的数据项进行字符串拼接，这时就需要用到group_concat()函数。

1、基本用法

group_concat()完整语法如下：

group_concat([DISTINCT] 要连接的字段 [Order BY ASC/DESC 排序字段] [Separator '分隔符'])

通过 distinct可以去掉重复值，order by进行排序，separator指定分隔符，默认为逗号。

user 表

![image.png](https://blog.alonesky.com/storage/article/2022/09/09/r5mIux1m7jsNli41QcwtEfMe3O3svnDTeOnjLCxY.png)

address 表

![image.png](https://blog.alonesky.com/storage/article/2022/09/09/nRhGlXQOyoG1CnAXQqEXmpdYPTIUGos0t1MW1YWo.png)

user与address为一对多关系，现在以user_id进行group by分组，对数据项city进行字符串拼接，写法如下：

```
select u.id, u.name, group_concat(ad.city) as city

from user u inner join address ad on u.id = ad.user_id

group by u.id
```

查询结果如下：

![image.png](https://blog.alonesky.com/storage/article/2022/09/09/wuPlVQHIP1BUe3KX9cBdqzO0L14eRMeWf9abw8ZC.png)

2、distinct 去重

从上文可以看到 id=2的数据项有两个广州市

```
select u.id, u.name, group_concat(distinct ad.city) as city

from user u inner join address ad on u.id = ad.user_id

group by u.id
```

结果如下：

![image.png](https://blog.alonesky.com/storage/article/2022/09/09/ZBGwdQHG0Ia35sHqLe35YutEKCbMbA4yQfR0Y6Qp.png)

3、 order by 排序

city按照以倒序的顺序排列

```
select u.id, u.name, group_concat(distinct ad.city order by ad.city desc) as city

from user u inner join address ad on u.id = ad.user_id

group by u.id
```

结果如下：

![image.png](https://blog.alonesky.com/storage/article/2022/09/09/rWzXIXjFjJy66ftgkw6ThICbyXB7VMenLFoQnpZK.png)


4、separator 指定分隔符

默认分隔符为逗号，这里指定短横线 --

```
select u.id, u.name, group_concat(distinct ad.city order by ad.city desc separator '--') as city

from user u inner join address ad on u.id = ad.user_id

group by u.id
```

结果如下：

![image.png](https://blog.alonesky.com/storage/article/2022/09/09/ivVhi4g18GtyDET8MUG9cEWGGABMgfKAalTtX7wM.png)

5、多字段拼接

以上的例子是基于单数据列演示的，如果需要多个数据列拼成一个字段返回的话，写法也很简单，如下所示

group_concat(数据列1，'分隔符',数据列2，Separator '分隔符')

下面是简单的例子

-- 拼接city与address

```
select u.id, u.name, group_concat(ad.city,'--',ad.address SEPARATOR ';') as cityAddress

from user u inner join address ad on u.id = ad.user_id

group by u.id
```

结果如下：

![image.png](https://blog.alonesky.com/storage/article/2022/09/09/C2YKHz4Qe3GbOnRNuwQFCeBJ2ERkXZbg8OWI1txQ.png)

原文链接：https://blog.csdn.net/weixin_28719385/article/details/113219491