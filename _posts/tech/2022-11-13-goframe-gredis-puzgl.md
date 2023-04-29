---
layout: post
title: GoFrame gredis配置文件及配置方法
category: Python
keywords: 
tags: 常识 Python
description: 
---

> goframe框架支持两种方式来管理redis配置和获取redis对象，一种是通过配置文件+单例对象的方式；一种是模块化通过配置管理方法及对象创建方法

一、配置文件（推荐）

绝大部分情况下推荐使用g.Redis单例方式来操作redis。

推荐使用配置文件来管理Redis配置，在config.toml中的配置示例如下：
```
# Redis数据库配置
[redis]
    default = "127.0.0.1:6379,0"
    cache   = "127.0.0.1:6379,1,123456?idleTimeout=600"

```
其中，Redis的配置格式为：
> host:port[,db,pass?maxIdle=x&maxActive=x&idleTimeout=x&maxConnLifetime=x]

> 其中的default和cache分别表示配置分组名称，我们在程序中可以通过该名称获取对应配置的redis单例对象。

| 配置项名称 | 是否必须 | 默认值 | 说明 |
|:----------:|:--------:|:------:|:-----|
| host | 是 | - | 地址 |
| port | 是 | - | 端口 |
| db | 否 | 0 | 数据库 |
| pass | 否 | - | 授权密码 |
| maxIdle | 否 | 10 | 允许闲置的连接数(0表示不限制) |
| maxActive | 否 | 100 | 最大连接数量限制(0表示不限制) |
| idleTimeout | 否 | 10 | 连接最大空闲时间(单位秒,不允许设置为0) |
| maxConnLifetime | 否 | 30 | 连接最长存活时间(单位秒,不允许设置为0) |
| tls | 否 | false | 是否使用TLS认证 |
| skipVerify | 否 | false | 通过TLS连接时，是否禁用服务器名称验证 |

不传递分组名称时，默认使用redis.default配置分组项来获取对应配置的redis客户端单例对象。 执行后，输出结果为：

使用示例：
```
package main
import (
    "fmt"
    "github.com/gogf/gf/frame/g"
    "github.com/gogf/gf/util/gconv"
)
func main() {
    g.Redis().DoVar("SET", "k", "v")
    v, _ := g.Redis().DoVar("GET", "k")
    fmt.Println(v.String())
}
```

二、配置方法（高级）

由于gf是模块化的框架，除了可以通过耦合且便捷的g模块来自动解析配置文件并获得单例对象之外，也支持开发者模块化使用gredis包。

但是这种用法对开发者的要求相对较高。

gredis提供了全局的分组配置功能，相关配置管理方法如下：

```
func SetConfig(config Config, name ...string)
func GetConfig(name ...string) (config Config, ok bool)
func RemoveConfig(name ...string)
func ClearConfig()
```

其中name参数为分组名称，即为通过分组来对配置对象进行管理，我们可以为不同的配置对象设置不同的分组名称，随后我们可以通过Instance单例方法获取redis客户端操作对象单例。

```
func Instance(name ...string) *Redis
```

使用示例：

```
package main
import (
    "fmt"
    "github.com/gogf/gf/database/gredis"
    "github.com/gogf/gf/util/gconv"
)
var (
    config = gredis.Config{
        Host : "127.0.0.1",
        Port : 6379,
        Db   : 0,
    }
)
func main() {
    group := "test"
    gredis.SetConfig(&amp;config, group)
    redis := gredis.Instance(group)
    defer redis.Close()
    _, err := redis.Do("SET", "k", "v")
    if err != nil {
        panic(err)
    }
    r, err := redis.Do("GET", "k")
    if err != nil {
        panic(err)
    }
    fmt.Println(gconv.String(r))
}
```

原文链接：[https://www.jb51.net/article/251268.htm](https://www.jb51.net/article/251268.htm)