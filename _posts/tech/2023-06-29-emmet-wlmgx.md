---
layout: post
title: Emmet语法总结
category: Plugin
keywords: Common Plugin Tool
tags: Common Plugin Tool
description: 
---

一、 Emmet简介

Emmet是一个Web开发工具，用于加快HTML和CSS代码的编写速度。使用Emmet能够通过简短的表达式生成HTML或CSS代码片段。另外，截至2022年，主流的编辑器工具如Visual Studio Code、WebStorm都已经集成了Emmet工具，无需手动安装即可使用。

二、 HTML语法

1. 初始化HTML结构

    输入!再按Tab键即可生成HTML初始化结构：

    ```
    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
    </head>
    <body>
      
    </body>
    </html>
    ```

2. 生成带有id的标签

    使用操作符#即可生成一个带有id的标签，如输入div#main可生成如下代码片段：

    ```
    <div id="main"></div>
    ```

    当标签为div时，还可以省略div标签，直接输入#main即可生成与上面相同的代码片段

3. 生成带有class的标签

    使用操作符.即可生成一个带有class的标签，如输入div.main可生成如下代码片段：

    ```
    <div class="main"></div>
    ```

    类似的，当标签为div时，还可以省略div标签，直接输入.main即可生成与上面相同的代码片段

4. 生成带有属性的标签

    使用操作符[]即可生成一个带有属性的标签，如输入div[name=syz age=18]可生成如下代码片段：

    ```
    <div name="syz" age="18"></div>
    ```

5. 标签属性值数字编号

    使用操作符$即可生成带有数字编号的标签属性值，如输入ul>li.className$*3可生成如下代码片段：

    ```
    <ul>
      <li class="className1"></li>
      <li class="className2"></li>
      <li class="className3"></li>
    </ul>
    ```

6. 生成标签内文本

    使用操作符{}即可生成带文本内容的标签，如输入div{文本内容}可生成如下代码片段：

    ```
    <div>文本内容</div>
    ```

7. 子节点生成

    使用操作符>即可生成一对父子节点，如输入div>span可生成如下代码片段：

    ```
    <div><span></span></div>
    ```

8. 兄弟节点生成

    使用操作符+即可生成一对兄弟节点，如输入div+div可生成如下代码片段：

    ```
    <div></div>
    <div></div>
    ```

9. 父级兄弟节点生成

    使用操作符^即可生成一个父级兄弟节点，父级兄弟节点生成通常与子节点生成同时使用，
    如输入div>span^p可生成如下代码片段：

    ```
    <div><span></span></div>
    <p></p>
    ```

    顾名思义，当使用子节点生成使当前上下文处于子节点时，可以通过^操作符使上下文回到父节点处：

    还可以使用多个^操作符使语境处于多个父级中，如div>ul>li^^p可生成如下代码片段：

    ```
    <div>
      <ul>
        <li></li>
      </ul>
    </div>
    <p></p>
    ```

10. 重复节点生成

    使用操作符*即可生成重复的节点，如输入div*3可生成如下代码片段：

    ```
    <div></div>
    <div></div>
    <div></div>
    ```

11. 节点分组

    使用操作符()即可将部分节点分组形成一个整体，将()内的节点与外面节点隔离，避免产生嵌套关系，
    如输入div>(ul>li)+p可生成如下代码片段：

    ```
    <div>
      <ul>
        <li></li>
      </ul>
      <p></p>
    </div>
    ```

    在这个例子中(ul>li)可看作一个整体，这里用字母A表示，则表达式转换为div>A+p，
    这时p标签就为A的兄弟节点。若不加()，输入div>ul>li+p则生成的代码片段如下：

    ```
    <div>
      <ul>
        <li></li>
        <p></p>
      </ul>
    </div>
    ```

    可以发现p标签变成了li标签的兄弟节点

三、CSS语法
    
本文对Emmet中的CSS语法部分仅做简单介绍并列举一些常用的方法，若读者想详细了解请参阅
官方文档[CSS Abbreviations](https://docs.emmet.io/css-abbreviations)。

1. width和height

    输入w100即可生成width: 100px，输入w100%即可生成width: 100%；height同理。

2. margin和padding

    输入m10即可生成margin: 10px，当要分别设置四个方向的属性值时，
    输入m10px20px30px40px即可生成代码片段margin: 10px 20px 30px 40px；padding同理。

3. 属性值生成

    (1) 输入fwb即可生成代码片段font-weight: bold；

    (2) 输入lh20px即可生成代码片段line-height: 20px；

    (3) 输入df即可生成代码片段display: flex；

    (4) 输入jcc即可生成代码片段justify-content: center；

    (5) 输入aic即可生成代码片段align-items: center；

    (6) 输入poa即可生成代码片段position: absolute；

    (7) 输入tac即可生成代码片段text-align: center；

    (8) ......

根据上面的例子，其实可以发现规律，Emmet中用首字母+具体值的形式生成CSS代码片段，这里就不一一列举了，读者可以在编辑器中自行尝试一下。

原文链接：[https://blog.csdn.net/syzdev/article/details/123826365](https://blog.csdn.net/syzdev/article/details/123826365)