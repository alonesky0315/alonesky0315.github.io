---
layout: post
title: OpenSeadragon插件使用
category: JavaScript
keywords: Common JavaScript HTML
tags: Common JavaScript HTML
description: 
---

#### OpenSeadragon插件使用

```html
<script src="openseadragon.min.js"></script>
<div id="openseadragonBox"style="height:100vh;width:100vw"></div>
<script type="text/javascript">
    var viewer = OpenSeadragon({
        // 指定显示图像的容器元素的 ID
        id: "openseadragonBox",
        // 设置图标和控件所需图像文件的路径
        prefixUrl: "images/",
        // 定义要显示的图像源
        tileSources: {
            type: 'image',
            url: 'bg/1h6dl8081_thumb.jpeg'
        },
        // 不显示导航器
        showNavigator: false,
        // 设置导航器的大小比例
        navigatorSizeRatio: 0.2,
        // 规定最小缩放级别
        minZoomLevel: 0.1,
        // 最大缩放级别
        maxZoomLevel: 10.0,
        // 设置初始缩放级别
        initialZoomLevel: 1.0,
        // 设置可见性比例
        visibilityRatio: 0.5,
        // 禁止水平循环
        wrapHorizontal: false,
        // 禁止垂直循环
        wrapVertical: false,
    });
    // 通过 addHandler 方法监听 open-failed 事件
    viewer.addHandler('open-failed', function (event) {
        console.error('图像加载失败:', event.source);
    });
    // 通过 addHandler 方法监听 zoom 事件
    viewer.addHandler('zoom', function (event) {
        console.log('当前缩放级别: ', event.zoom);
    });
</script>
```