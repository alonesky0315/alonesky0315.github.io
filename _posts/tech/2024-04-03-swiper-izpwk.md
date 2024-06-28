---
layout: post
title: swiper全屏鼠标不能滚动问题
category: JavaScript
keywords: JavaScript
tags: JavaScript
description: 
---

> swiper全屏鼠标不能滚动,网上很多都是直接用swiper.update()

```
// 全屏
var fullpage_swiper = new Swiper('.fullpage_swiper', {
  direction: 'vertical', // 垂直方向，默认水平方向
  speed: 1200,
  parallax: true,
  mousewheel: true,// 开启鼠标滚轮控制滑动
  mousewheel: {
    releaseOnEdges: true,// PC端释放滑动    
  },
  // initialSlide: 2,
  on: {
    init: function () {
      swiperAnimateCache(this); // 隐藏动画元素
      swiperAnimate(this); // 初始化完成开始动画
    },
    slideChangeTransitionEnd: function () {
      fullpage_swiper.mousewheel.enable();
      swiperAnimate(this); // 每个slide切换结束时也运行当前slide动画
      var idx = this.realIndex;
      if (idx > 0) {
        $('.head').addClass('bgw').find('img').attr('src', '/assets/default/images/colour_logo.png');
      } else {
        $('.head').removeClass('bgw').find('img').attr('src', '/assets/default/images/logo.png');
      }
      if (idx == 1) {
        $('.dist-num').countUp();
      }
      if (fullpage_swiper.isEnd) {
        $('.foot_icp_new').show();
      } else {
        $('.foot_icp_new').hide();
      }
    },
  },
  pagination: {
    el: ".fullpage_swiper>.fullpage_swiper_pagination",
    clickable: true,
  },
});
```

参考链接：[https://www.cnblogs.com/aknife/p/14034401.html](https://www.cnblogs.com/aknife/p/14034401.html)