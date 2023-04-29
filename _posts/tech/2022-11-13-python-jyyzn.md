---
layout: post
title: python将数据插入数据库
category: Python
keywords: 
tags: Python
description: 
---

> python将数据插入数据库的方法：
> 
> 首先读入数据并建立数据库连接；
> 
> 然后创建数据库；
> 
> 接着执行插入数据语句，迭代读取每行数据；
> 
> 最后关闭数据库连接即可。

示例数据

![image.png](https://blog.alonesky.com/storage/article/2022/11/13/1Bcr0SNtXlQRtOsDmsOcifx7QWUE5MBeJznQp2TC.png)

```
#导入需要使用到的数据模块
import pandas as pd
import pymysql
 
#读入数据
filepath = './excel.xls'
data = pd.read_excel(filepath)
 
#建立数据库连接
db = pymysql.connect(host='localhost', port=3306, user='root', passwd='root', db='demo',charset='utf8')
#获取游标对象
cursor = db.cursor()
#创建数据库，如果数据库已经存在，注意主键不要重复，否则出错
try:
    cursor.execute('create table sale(id int primary key AUTO_INCREMENT,date datetime, sale float )')
except:
    print('数据库已存在！')
 
#插入数据语句
query = """insert into sale (id, date, sale) values (%s,%s,%s)"""
 
#迭代读取每行数据
#values中元素有个类型的强制转换，否则会出错的
#应该会有其他更合适的方式，可以进一步了解
for r in range(0, len(data)):
    id = data.loc[r,'Id']
    date = data.loc[r,'date']
    sale = data.loc[r,'sale']
    values = (int(id), str(date), float(sale))
    cursor.execute(query, values)
 
#关闭游标，提交，关闭数据库连接
#如果没有这些关闭操作，执行后在数据库中查看不到数据
cursor.close()
db.commit()
db.close()
 
#重新建立数据库连接
db = pymysql.connect(host='localhost', port=3306, user='root', passwd='root', db='demo',charset='utf8')
cursor = db.cursor()
#查询数据库并打印内容
cursor.execute('''select * from sale''')
results = cursor.fetchall()
for row in results:
    print(row)
#关闭
cursor.close()
db.commit()
db.close()
```

参考链接：

python将数据插入数据库的方法：
[https://www.cnblogs.com/ludundun/p/14075967.html](https://www.cnblogs.com/ludundun/p/14075967.html)

AttributeError: ‘DataFrame‘ object has no attribute ‘ix‘：
[https://blog.csdn.net/yuan2019035055/article/details/124558014](https://blog.csdn.net/yuan2019035055/article/details/124558014)