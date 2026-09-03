---
layout: post
title: OpenClaw对接 QQ 机器人
category: Article
keywords: Common Article
tags: Common Article
description: 
---

#### OpenClaw对接 QQ 机器人

##### 对接 QQ 机器人的两种主流方案

方案 A： 官方 QQ 机器人（合规稳定，推荐）

1. 安装 QQ 插件
```
openclaw plugins install @sliverp/qqbot
```
2. 在 QQ 开放平台创建机器人
 访问 https://q.qq.com/ 创建机器人，获取 AppID 和 AppSecret。
3. 配置 OpenClaw
 方式一（Web 控制台）：访问 http://127.0.0.1:18789，进入「渠道管理」>「QQ」，填写 AppID 和 AppSecret。
 方式二（命令行）：
```
openclaw channels add --channel qqbot --token "你的AppID:你的AppSecret"
```
4. 重启网关
```
openclaw gateway restart
```

方案 B： 个人 QQ 号改造（极客玩法）

通过第三方框架（如 NapCat，原 go-cqhttp），让你的个人 QQ 号变身机器人，体验更自然，但需注意规避风控。   
安装并配置 NapCat 等框架，使其提供 WebSocket 服务。   
安装对应的 OpenClaw 插件，如 @izhimu/qq。   
在 OpenClaw 中配置 WebSocket 地址，连接到 NapCat。