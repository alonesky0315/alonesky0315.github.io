---
layout: post
title: Swiper实现无缝滚动
category: JavaScript
keywords: Common JavaScript HTML
tags: Common JavaScript HTML
description: 
---

> 利用Swiper实现无缝滚动

导入swiper.min.js

```
<script type="text/javascript" src="js/swiper.min.js?v=8.4.7"></script>
```

html代码

```
<div class="honor_swiper">
    <div class="swiper-wrapper">
        <div class="swiper-slide">
            <a href="javascript:;">
                <div class="img_bg">
                    <img src="/swiper-slide.jpg" title="swiper-slide" alt="swiper-slide.jpg">
                </div>
                <span>swiper-slide</span>
            </a>
        </div>
    </div>
    <div class="swiper-button-next"></div>
    <div class="swiper-button-prev"></div>
</div>
```

初始化swiper.min.js

```
var honor_swiper = new Swiper(".honor_swiper", {
     speed: 4000,
     loop: true,
     slidesPerView: 'auto',
     freeMode: true,
     autoplay: {
          delay: 0,
          stopOnLastSlide: false,
          disableOnInteraction: false,
          loopPreventsSlide: true,
     },
     navigation: {
          nextEl: ".swiper-button-next",
          prevEl: ".swiper-button-prev",
     },
  });
$('.honor_swiper').on('mouseenter', function(e){
     honor_swiper.stopAutoplay();
})
$('.honor_swiper').on('mouseleave', function(e){      
     honor_swiper.startAutoplay();
});

```

css样式

```
.honor_swiper .swiper-wrapper {
     -webkit-transition-timing-function: linear;
     -moz-transition-timing-function: linear;
     -ms-transition-timing-function: linear;
     -o-transition-timing-function: linear;
     transition-timing-function: linear;
}
```

参考链接：[https://blog.csdn.net/m0_52761651/article/details/127065428](https://blog.csdn.net/m0_52761651/article/details/127065428)