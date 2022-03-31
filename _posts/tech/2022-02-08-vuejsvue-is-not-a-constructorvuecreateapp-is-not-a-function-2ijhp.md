---
layout: post
title: vue.js提示Vue is not a constructor或Vue.createApp is not a function解决方法
category: JavaScript
keywords: 
tags: 
description: 
---

> 打开浏览器F12，它提示我：Vue is not a constructor

```
/*Vue 2*/
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
/*Vue 3*/
Vue.createApp({
  data() {
    return {
      items: [{ message: 'Foo' }, { message: 'Bar' }]
    }
  }
}).mount('#array-rendering')
```
参考链接：

[https://blog.csdn.net/Charlesix59/article/details/118819764](https://blog.csdn.net/Charlesix59/article/details/118819764)