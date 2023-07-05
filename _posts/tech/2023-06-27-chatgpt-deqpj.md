---
layout: post
title: 搭建ChatGPT
category: Tool
keywords: Common Plugin Tool
tags: Common Plugin Tool
description: 
---

#### 搭建ChatGPT

> 利用GitHub上开源项目搭建ChatGPT

Nodejs版本： [https://github.com/Chanzhaoyu/chatgpt-web](https://github.com/Chanzhaoyu/chatgpt-web)

一、 前置要求

##### Node

`node` 需要 `^16 || ^18 || ^19` 版本（`node >= 14` 需要安装 [fetch polyfill](https://github.com/developit/unfetch#usage-as-a-polyfill)），使用 [nvm](https://github.com/nvm-sh/nvm) 可管理本地多个 `node` 版本

```
node -v
```

##### PNPM

如果你没有安装过 `pnpm`

```
npm install pnpm -g
```

##### 填写密钥

获取 `Openai Api Key` 或 `accessToken` 并填写本地环境变量 [跳转](#介绍)

```
# service/.env 文件

# OpenAI API Key - https://platform.openai.com/overview
OPENAI_API_KEY=

# change this to an `accessToken` extracted from the ChatGPT site's `https://chat.openai.com/api/auth/session` response
OPENAI_ACCESS_TOKEN=
```

二、 安装依赖

> 为了简便 `后端开发人员` 的了解负担，所以并没有采用前端 `workspace` 模式，而是分文件夹存放。如果只需要前端页面做二次开发，删除 `service` 文件夹即可。

##### 后端

进入文件夹 `/service` 运行以下命令

```
pnpm install
```

##### 前端

根目录下运行以下命令

```
pnpm bootstrap
```

三、 测试环境运行
##### 后端服务

进入文件夹 `/service` 运行以下命令

```
pnpm start
```

##### 前端网页

根目录下运行以下命令

```
pnpm dev
```

##### 环境变量

`API` 可用：

- `OPENAI_API_KEY` 和 `OPENAI_ACCESS_TOKEN` 二选一
- `OPENAI_API_MODEL`  设置模型，可选，默认：`gpt-3.5-turbo`
- `OPENAI_API_BASE_URL` 设置接口地址，可选，默认：`https://api.openai.com`
- `OPENAI_API_DISABLE_DEBUG` 设置接口关闭 debug 日志，可选，默认：empty 不关闭

`ACCESS_TOKEN` 可用：

- `OPENAI_ACCESS_TOKEN`  和 `OPENAI_API_KEY` 二选一，同时存在时，`OPENAI_API_KEY` 优先
- `API_REVERSE_PROXY` 设置反向代理，可选，默认：`https://ai.fakeopen.com/api/conversation`，[社区](https://github.com/transitive-bullshit/chatgpt-api#reverse-proxy)（注意：只有这两个是推荐，其他第三方来源，请自行甄别）

通用：

- `AUTH_SECRET_KEY` 访问权限密钥，可选
- `MAX_REQUEST_PER_HOUR` 每小时最大请求次数，可选，默认无限
- `TIMEOUT_MS` 超时，单位毫秒，可选
- `SOCKS_PROXY_HOST` 和 `SOCKS_PROXY_PORT` 一起时生效，可选
- `SOCKS_PROXY_PORT` 和 `SOCKS_PROXY_HOST` 一起时生效，可选
- `HTTPS_PROXY` 支持 `http`，`https`, `socks5`，可选
- `ALL_PROXY` 支持 `http`，`https`, `socks5`，可选


Go版本： [https://github.com/869413421/chatgpt-web](https://github.com/869413421/chatgpt-web) 

> ##### windows
> 
> 1.下载压缩包解压
> 
> 2.复制文件中config.dev.json更改为config.json
> 
> 3.将config.json中的api_key替换为自己的
> 
> 4.双击exe运行，启动服务
> 
> ##### linux
> 
> $ tar xf chatgpt-web-v0.0.2-darwin-arm64.tar.gz # 解压
> 
> $ cd chatgpt-web-v0.0.2-darwin-arm64
> 
> $ cp config.dev.json # 根据情况调整配置文件内容
> 
> $ ./chatgpt-web  # 直接运行
> 
> ##### 如果要守护在后台运行
> 
> $ nohup ./chatgpt-web &> run.log &
> 
> $ tail -f run.log