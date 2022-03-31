---
layout: post
title: 小程序单选按钮选中与取消状态切换—radio
category: 小程序
keywords: 
tags: 小程序
description: 
---

<label class="fr font checked-lable" catchtap='checkedTap'>
   <radio checked="{checked}" color='#deab8a' />完全OK
</label>

data: {
    checked: false,
},
 
//是否
  checkedTap: function() {
    var checked = this.data.checked;
    this.setData({
      "checked": !checked
    })
  }