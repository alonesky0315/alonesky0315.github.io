---
layout: post
title: vue-datepicker 参数设置
category: JavaScript
keywords: Common JavaScript Plugin
tags: Common JavaScript Plugin
description: 
---

> vue-datepicker 参数设置

```html
<div class="form-group row">
    <label class="col-sm-2 col-form-label">{{ $t('form.datetime') }}</label>
    <div class="col-sm-10">
    <date-picker :date="startTime" :option="option"></date-picker>
    </div>
</div>
```

```js
import DatePicker from 'vue-datepicker'

export default {
    data () {
        return {
            option: {
                type: 'day',
                format: 'YYYY-MM-DD',
                week: ['一', '二', '三', '四', '五', '六', '日'],
                month: ['一月', '二月', '三月', '四月', '五月', '六月', '七月', '八月', '九月', '十月', '十一月', '十二月'],
                buttons: {
                    ok: '确定',
                    cancel: '取消'
                }
            }
        }
    }
}
```

参考链接：[https://gitee.com/minchao/vue-datepicker/blob/master/vue-datepicker.vue](https://gitee.com/minchao/vue-datepicker/blob/master/vue-datepicker.vue)