---
layout: post
title: vue-date-picker默认当前时间
category: JavaScript
keywords: 
tags: 常识 JavaScript
description: 
---

```
<div class="form-group row">
  <label class="col-sm-2 col-form-label">{{ $t('form.datetime') }}</label>
  <div class="col-sm-10">
    <date-picker :date="startTime" default-value="2020-04-08" :option="option">
		</date-picker>
  </div>
</div>
// 导入JS
import DatePicker from 'vue-datepicker'
mounted() {
    this.startTime.time=this.formatTime();
}
methods: {
    formatTime() {
      let date = new Date();
      let year = date.getFullYear();
      let month = date.getMonth() + 1;
      let day = date.getDate();
      let hour = date.getHours();
      let minute = date.getMinutes();
      let second = date.getSeconds();
      return [year, month, day].map(this.formatNumber).join('-') + ' ' + 
			[hour, minute, second].map(this.formatNumber).join(':')
    },
    formatNumber(n) {
      n = n.toString()
      return n[1] ? n : '0' + n
    },
}
```