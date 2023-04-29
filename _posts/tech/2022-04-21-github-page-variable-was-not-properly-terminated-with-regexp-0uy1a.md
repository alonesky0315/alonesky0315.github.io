---
layout: post
title: GitHub Page Variable '{{# }' was not properly terminated with regexp
category: JavaScript
keywords: 
tags: 常识 JavaScript
description: 
---

GitHub Page 的（一种）输入是 markdown 文件，输出是 HTML/CSS/JS 文件。

如果 markdown 文件包含代码块，且代码块中包含花括号 { 或 }，尤其是包含 {/\%\ 或 {{ 符号组合时，GitHub Page 会报错。

分别在代码块前后添加 {/\%\ raw /\%\} 和 {/\%\ endraw /\%\} 即可解决该问题。

GitHub Page 默认使用 Jekyll - Simple, blog-aware, static sites 来从 markdown 生成网页文件

Jekyll 支持 Liquid - Safe, customer-facing template language 标记语言

Liquid 的标记可分为三类

objects，{{ ... }}

tags，{/\%\ ... /\%\}

filters，{ ... | ...}

假设一个开启了 GitHub Page 的项目，其README.md 文件包含如下几行

```latex
\newcommand\abc{/\%\
  hello, abc.
}
```

这几行，在网站构建时会产生错误。在项目的设置页面，我们能看到类似这样的报错信息

![image.png](https://blog.alonesky.com/storage/article/2022/04/21/4yApGzJxksLlLNNPHTuMQqdYPx8aQZ74kBuu403H.png)

同时，在 GitHub 的账号邮箱里，能收到构建失败的邮件，关键词是

The tag `{/\%\` on line 18 in `README.md` was not properly closed with `/\%\}`.

根据帮助链接的建议，我们在本地构建，能得到更为详细的错误信息。

Liquid Exception: Liquid syntax error (line 18): Tag '{/\%\' was not properly terminated with regexp: /\%\}/ in README.md

搜索关键词，可以得到一些相似的问题求助

How to Ignore Liquid Syntax Errors · Issue #927 · Shopify/liquid
Ignore a specific tag in Jekyll
[GH-Pages] Jekyll cannot build a TeX code sample · Issue #7180 · jekyll/jekyll
这些信息，指向同一个解决方案：加 Liquid raw tag

```latex
{% raw %}\newcommand\abc{%
  hello, abc.
}{% endraw %}
```

结合本文开头提供的背景知识，错误的来源是，Jekyll 在构建网站时，将 LaTeX 代码片段中的 {% 符号组合识别为 Liquid tag 的起始部分。从列出的前两条问题求助中可以了解到，连续两个左侧花括号，也会带来构建错误。

不只是 LaTeX，只要 .md 文件中出现了与 Liquid 代码的三类标记相同的字符组合，这个问题就会触发。

根据第三条问题求助中 Jekyll 项目成员提供的信息，目前只能通过添加 Liquid raw tag 来避免错误。下个 Jekyll 的大版本会尝试带来更好的方案。

参考链接：[https://jekyllrb.com/docs/liquid/tags/#code-snippet-highlighting](https://jekyllrb.com/docs/liquid/tags/#code-snippet-highlighting)