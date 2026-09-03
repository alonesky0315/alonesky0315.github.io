---
layout: post
title: HTML 中的 meta 标签里的 og（Open Graph）协议字段
category: HTML
keywords: Common HTML
tags: Common HTML
description: 
---

HTML 中的 `meta` 标签里的 `og`（Open Graph）协议字段用于在社交媒体平台分享网页时，提供丰富的元数据，使分享的内容以更美观、信息更丰富的形式展示。以下是一些常见的 `og` 字段：

### 基本字段
- **og:title**
    - **说明**：定义网页的标题，通常显示在分享卡片的顶部，是吸引用户点击的重要信息。
    - **示例**：
```html
<meta property="og:title" content="HTML 中的 meta 标签里的 og（Open Graph）协议字段">
```
- **og:type**
    - **说明**：指定网页的类型，常见的类型有 `website`（普通网页）、`article`（文章）、`video.movie`（电影视频）等。不同的类型在社交媒体上的展示形式可能会有所不同。
    - **示例**：
```html
<meta property="og:type" content="article">
```
- **og:url**
    - **说明**：网页的规范 URL，当用户点击分享卡片时会跳转到该 URL。确保此 URL 是唯一且正确的，避免出现跳转错误。
    - **示例**：
```html
<meta property="og:url" content="https://blog.alonesky.com/html-meta-ogopen-graph-fixi9">
```
- **og:image**
    - **说明**：分享时显示的图片 URL，图片应具有较高的分辨率和吸引力，以提高分享的点击率。可以是 JPEG、PNG 或 GIF 格式。
    - **示例**：
```html
<meta property="og:image" content="https://blog.alonesky.com/storage/cover/2025/02/19/IB9bFfPF4M5lJpvzse8dBdx5ujb7TgKkmK1bybZH.jpg">
```
    - **可选参数**：
        - **og:image:secure_url**：如果图片使用 HTTPS 协议提供，可以使用此参数指定安全的图片 URL。
        - **og:image:type**：指定图片的 MIME 类型，如 `image/jpeg`。
        - **og:image:width** 和 **og:image:height**：分别指定图片的宽度和高度，以像素为单位。
```html
<meta property="og:image:secure_url" content="https://blog.alonesky.com/storage/cover/2025/02/19/IB9bFfPF4M5lJpvzse8dBdx5ujb7TgKkmK1bybZH.jpg">
<meta property="og:image:type" content="image/jpeg">
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">
```

### 其他常见字段
- **og:description**
    - **说明**：对网页内容的简短描述，显示在分享卡片的标题下方，帮助用户快速了解网页的主要内容。
    - **示例**：
```html
<meta property="og:description" content="日出的幻景,带给你不一样的体验">
```
- **og:site_name**
    - **说明**：网站的名称，通常显示在分享卡片的底部，让用户知道内容来自哪个网站。
    - **示例**：
```html
<meta property="og:site_name" content="日出的幻景">
```
- **og:locale**
    - **说明**：指定网页的语言和地区，格式为 `语言代码_地区代码`，如 `zh_CN` 表示中文（中国大陆）。
    - **示例**：
```html
<meta property="og:locale" content="zh_CN">
```
- **og:locale:alternate**
    - **说明**：如果网页有其他语言版本，可以使用此参数列出所有可用的语言和地区。
    - **示例**：
```html
<meta property="og:locale:alternate" content="en_US">
<meta property="og:locale:alternate" content="ja_JP">
```

### 特定类型字段（以 article 为例）
当 `og:type` 为 `article` 时，还可以使用以下特定字段：
- **article:published_time**
    - **说明**：文章的发布时间，格式为 ISO 8601 日期时间格式，如 `2024-10-01T12:00:00Z`。
    - **示例**：
```html
<meta property="article:published_time" content="2025-02-01T00:00:00Z">
```
- **article:modified_time**
    - **说明**：文章的最后修改时间。
    - **示例**：
```html
<meta property="article:modified_time" content="2025-03-01T00:00:00Z">
```
- **article:author**
    - **说明**：文章的作者，可以是作者的姓名或社交媒体账号的 URL。
    - **示例**：
```html
<meta property="article:author" content="小熊猫幺儿">
```
- **article:section**
    - **说明**：文章所属的分类或板块。
    - **示例**：
```html
<meta property="article:section" content="HTML">
```
- **article:tag**
    - **说明**：文章的标签，可使用多个标签，每个标签用逗号分隔。
    - **示例**：
```html
<meta property="article:tag" content="HTML,常识,meta 标签">
```