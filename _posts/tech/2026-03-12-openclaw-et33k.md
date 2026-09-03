---
layout: post
title: 完全卸载OpenClaw 龙虾
category: Article
keywords: Common Article
tags: Common Article
description: 
---

#### OpenClaw 龙虾完全卸载

1. 一键卸载
```
// 停止服务+删配置
openclaw uninstall --all --yes --non-interactive
// 卸载全局包
npm uninstall -g openclaw openclaw-cn --force
// 清缓存
npm cache clean --force
```
2. 删除.openclaw目录
```
#Linux 
rm -rf ~/.openclaw/workspace
#Window
Remove-Item -Force "$env:USERPROFILE\\.openclaw"
```
3. 卸载CLI本体
```
#如果是npm装的
npm rm -g openclaw
#如果是pnpm装的
pnpm remove -g openclaw
#如果是bun装的
bun remove -g openclaw
```
4. 删除后台服务
```
#Mac用户
// 强制停止服务
launchctl bootout gui/$UID/ai.openclaw.gateway
// 删除它的“开机自启”许可证
rm -f ~/Library/LaunchAgents/ai.openclaw.gateway.plist
#Linux用户
systemctl --user disable --now openclaw-gateway.service
rm -f ~/.config/systemd/user/openclaw-gateway.service
systemctl --user daemon-reload
#Window用户
schtasks /Delete /F /TN "OpenClaw Gateway"
Remove-Item -Force "$env:USERPROFILE\\.openclaw\gateway.cmd"
```

备注：   
- 删除macOS openclaw 桌面版
```
rm -rf /Applications/OpenClaw.app
```

**rm -rf 权限特别高，请再三确认后面内容是否正确，否则有系统瘫痪风险！！！**

参考链接： [https://mp.weixin.qq.com/s/C_rh9RErs_DEex5VyyzcYg](https://mp.weixin.qq.com/s/C_rh9RErs_DEex5VyyzcYg)