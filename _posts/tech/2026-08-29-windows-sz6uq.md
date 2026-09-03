---
layout: post
title: Windows 自带的快速移动文件命令
category: Common
keywords: Common Tool
tags: Common Tool
description: 
---

#### Windows 自带的快速移动文件命令

1. move 命令（CMD）
```
move 源文件 目标路径
```
示例：
```
# 移动单个文件
move "D:\test\file.txt" "E:\backup\"
# 移动并重命名
move "D:\test\file.txt" "E:\backup\newname.txt"
# 移动所有 .txt 文件
move "D:\test\*.txt" "E:\backup\"
```
2. robocopy 命令（推荐，更强大）
```
robocopy 源目录 目标目录 文件名 /MOVE
```
示例：
```
# 移动整个目录（含子目录）
robocopy "D:\test" "E:\backup" /E /MOVE
# 只移动 .txt 文件
robocopy "D:\test" "E:\backup" *.txt /MOVE
# /MOVE = 移动（复制后删除源）；/E = 包含空目录和子目录
```
3. PowerShell 的 Move-Item
```
Move-Item "D:\test\file.txt" "E:\backup\"
Move-Item "D:\test\*.txt" "E:\backup\"
```

##### 对比
| 命令 |速度 | 跨盘符 | 适用场景 |
| - | - | - | - |
| move | 快 | 同盘秒移、跨盘复制删除 | 简单移动 |
| robocopy | 最快 | 支持断点续传、多线程 | 大批量/大文件 |
| Move-Item | 中 | 支持 | 脚本化场景 |

##### 关键点：同盘符内移动是"秒移"（只改路径不复制数据），跨盘符必须复制+删除。大批量文件推荐用 robocopy /MOVE，速度最快且稳定。

##### move 命令参数
```
move [/Y | /-Y] [源] [目标]
```
| 参数 | 说明 |
| - | - |
| /Y | 取消确认提示，直接覆盖同名文件 |
| /-Y | 强制确认提示（默认行为）

#### 详细参数

move 参数很少，主要就这两个。跨盘符移动时不能用 /Y 批量覆盖。

##### robocopy 常用参数（最丰富）
复制选项

| 参数 | 说明 |
| - | - |
| /S | 复制子目录，不包含空目录 |
| /E | 复制子目录，包含空目录（最常用） |
| /MOVE | 移动文件（复制后删除源） |
| /COPY:DAT | 复制 Data/Attributes/Timestamps（默认） |
| /COPYALL | 复制所有信息（含 ACL、所有者等） |
| /NOCOPY | 不复制内容（只做目录结构镜像） |

覆盖与重试

| 参数 | 说明 |
| - | - |
| /IS | 包含相同文件（默认跳过） |
| /IT | 包含已修改的相同文件 |
| /R:n | 失败重试次数（默认 100 万次，建议 /R:3） |
| /W:n | 重试间隔秒数（默认 30，建议 /W:1） |
| /XO | 排除较旧的文件（只更新新文件） |
| /XX | 排除源中已不存在的文件 |

性能与日志

| 参数 | 说明 |
| - | - |
| /MT:n | 多线程（n=1~128，默认 8，大幅提速） |
| /LOG:文件 | 输出日志到文件 |
| /NP | 不显示进度百分比（日志更干净） |
| /NDL | 不显示目录名（减少输出） |
| /TEE | 同时输出到屏幕和日志 |

过滤

| 参数 | 说明 |
| - | - |
| /XF *.tmp | 排除指定文件 |
| /XD temp | 排除指定目录 |
| /MIN:n | 只复制大于 n 字节的文件 |
| /MAX:n | 只复制小于 n 字节的文件 |
| /MINAGE:n | 只复制 n 天后修改的文件 |
| /MAXAGE:n | 只复制 n 天前修改的文件 |

实组合示例：
```
# 高速多线程移动 + 3次重试 + 日志
robocopy "D:\src" "E:\dst" /E /MOVE /MT:16 /R:3 /W:1 /LOG:move.log /NP

# 只移动 7 天内修改过的 .jpg
robocopy "D:\src" "E:\dst" *.jpg /S /MOVE /MINAGE:7

# 排除 .tmp 文件和 temp 目录
robocopy "D:\src" "E:\dst" /E /MOVE /XF *.tmp /XD temp
```
##### Move-Item 参数
```
Move-Item [-Path] <路径> [-Destination] <目标> [-Force] [-Filter] [-Include] [-Exclude] [-WhatIf] [-Confirm]
```
| 参数 | 说明 |
| - | - |
| -Force | 强制覆盖只读文件、隐藏文件 |
| -Filter | 按通配符过滤（如 *.txt） |
| -Include | 包含指定项（需配合 * 路径） |
| -Exclude | 排除指定项 |   
| -WhatIf | 预演不执行（先看会发生什么） |
| -Confirm | 每步确认 |
| -PassThru | 输出移动后的对象 |

示例：
```
# 强制移动所有 .log，排除 error.log
Move-Item "D:\src\*.log" "E:\logs\" -Force -Exclude "error.log"

# 预演（不实际执行）
Move-Item "D:\src\*" "E:\dst\" -WhatIf
```

选择建议

| 场景 | 推荐 |
| - | - |
| 单个/少量文件，同盘 | move |
| 大批量、大文件、跨盘 | robocopy /MOVE /MT:16 /R:3 |
| 需要精细过滤、脚本化 | Move-Item |
| 需要日志和容错 | robocopy（独有） |   

robocopy 是 Windows 下移动文件最强工具，参数最多，推荐优先使用。