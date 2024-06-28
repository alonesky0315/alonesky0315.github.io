---
layout: post
title: Swiper应用
category: JavaScript
keywords: JavaScript HTML
tags: JavaScript HTML
description: 
---

> Swiper 4.5.3

1. 多行轮播
```js
var honors_swiper = new Swiper(".honors_swiper",{
    slidesPerView: 5,
    slidesPerColumn: 2,
    // slidesPerColumnFill: 'row',
    slidesPerGroup: 8,
    spaceBetween: 0,
    breakpoints: {
    768: {
        slidesPerView: 2,
    },
    },
    navigation: {
    nextEl: ".honors_swiper>.swiper-button-next",
    prevEl: ".honors_swiper>.swiper-button-prev",
    },
});
```
```html
<div class="swiper qualifications_honors_swiper">
    <div class="swiper-wrapper">
        {cms:arclist id="honor_item" channel="6" type="sons" flag="recommend" orderby="weigh desc" row="16"}
        <div class="swiper-slide wow fadeInUp">
            <div class="qualifications_honors_item">
                <img src="{$honor_item.image}" alt="{$honor_item.title}">
                <span class="qualifications_honors_title"></span>
            </div>
            <div class="clearfix"></div>
        </div>
        {/cms:arclist}
    </div>
    <div class="swiper-button-next"></div>
    <div class="swiper-button-prev"></div>
</div>
```

2. 全屏
```js
var fullpage_swiper = new Swiper(".fullpage_swiper", {
    direction: "vertical", //垂直方向，默认水平方向
    speed: 1200,
    parallax: true,
    mousewheel: true, // 开启鼠标滚轮控制滑动
    mousewheel: {
      releaseOnEdges: true, // PC端释放滑动
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
        var idx = this.realIndex;// 当前slide的index
        if (idx == 1) {
         // 数字滚动
          $(".dist-num").countUp();
        }
        if (fullpage_swiper.isEnd) {// 轮播结束
          $(".foot_icp_new").show();
        } else {
          $(".foot_icp_new").hide();
        }
      },
    },
    pagination: {
      el: ".fullpage_swiper>.fullpage_swiper_pagination",
      clickable: true,
    },
});
```
```html
<div class="swiper fullpage_swiper">
    <div class="swiper-wrapper">
        <div class="section swiper-slide" id="section0">
            <div class="g-bg" data-swiper-parallax="60%">
                <div class="video_play"></div>
                <video loop controls preload="auto" poster="../assets/default/images/video.jpg" playsinline="true"
                    x5-playsinline="true" webkit-playsinline="true" x5-video-player-type="h5-page"
                    x5-video-orientation="h5" x5-video-player-fullscreen="true">
                    <source src="../assets/default/images/video.mp4" type="video/mp4">
                </video>
            </div>
        </div>
    </div>
</div>
```

3. 带缩略图轮播
```js
if ($(".scenarios_swiper").length > 0) {
    var scenarios_thumb_swiper = new Swiper(".scenarios_thumb_swiper", {
      spaceBetween: 28,
      slidesPerView: "auto",
      autoplay: true,
      loop: true,
      watchSlidesVisibility: true,
      watchSlidesProgress: true,
    });
    var scenarios_swiper = new Swiper(".scenarios_swiper", {
      spaceBetween: 28,
      autoplay: true,
      loop: true,
      navigation: {
        nextEl: ".scenarios_swiper>.swiper-button-next",
        prevEl: ".scenarios_swiper>.swiper-button-prev",
      },
      thumbs: {
        swiper: scenarios_thumb_swiper,
      },
    });
}
```
```html
<div class="swiper-container scenarios_swiper">
    <div class="swiper-wrapper">
        {cms:blocklist id="aa_item" name="application_area"
        field="title,url,image,wap_thumb,begintime,endtime,content" orderby="weigh desc"}
        <div class="swiper-slide"
            style="background: url({$aa_item.wap_thumb}) no-repeat center;background-size: cover;">
            <div class="container">
                <div class="catname_box">
                    <span class="catname">应用场景</span>
                </div>
                <div class="scenarios_box">
                    <span class="scenarios_title">{$aa_item.title}</span>
                    <div class="scenarios_content">{$aa_item.content}</div>
                </div>
                <a href="/yingyonglingyu.html" class="more_info"><i>探索更多</i></a>
            </div>
        </div>
        {/cms:blocklist}
    </div>
    <div class="swiper-button-next"></div>
    <div class="swiper-button-prev"></div>
</div>
<div class="swiper-container scenarios_thumb_swiper">
    <div class="swiper-wrapper">
        {cms:blocklist id="aa_thumb_item" name="application_area"
        field="title,url,image,thumb,begintime,endtime,content" orderby="weigh desc"}
        <div class="swiper-slide">
            <span>{$aa_thumb_item.title}</span>
            <div class="img">
                <img src="{$aa_thumb_item.image}" />
            </div>
        </div>
        {/cms:blocklist}
    </div>
</div>
```

4. 调整切换时间和自动切换间隔
```
speed:切换速度，即slider自动滑动开始到结束的时间（单位ms），也是触摸滑动时释放至贴合的时间。
delay:自动切换的时间间隔，单位ms
```