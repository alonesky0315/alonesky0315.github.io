---
layout: post
title: Fastadmin fieldlist追加动态时间选择
category: JavaScript
keywords: 
tags: 常识 JavaScript HTML
description: 
---

Fastadmin fieldlist追加动态时间选择

```html
<div class="form-group">
    <label class="control-label col-xs-12 col-sm-2">{:__('Starttime')}:</label>
    <div class="col-xs-12 col-sm-8">
        <dl class="fieldlist" rel="0" data-name="opens" data-template="openstpl">
            <dd>
                <ins>开始时间</ins>
                <ins>结束时间</ins>
            </dd>
            <dd>
                <a href="javascript:;" class="append btn btn-sm btn-success">
                    <i class="fa fa-plus"></i>追加
                </a>
            </dd>
            <textarea name="opens" class="form-control hide" cols="30" rows="5">{$row.opens}</textarea>
        </dl>
    </div>
</div>
<script type="text/html" id="openstpl">
    <dd class="form-inline">
        <input type="text" name="row[<%=name%>][<%=index%>][starttime]" data-date-format="HH:mm" class="form-control datetimepicker" value="<%=row['starttime']%>" size="10">
        <input type="text" name="row[<%=name%>][<%=index%>][endtime]" data-date-format="HH:mm" class="form-control datetimepicker" value="<%=row['endtime']%>" size="10">
        <span class="btn btn-sm btn-danger btn-remove"><i class="fa fa-times"></i></span> <span class="btn btn-sm btn-primary btn-dragsort"><i class="fa fa-arrows"></i></span>
    </dd>
</script>

```

```js
edit: function () {
    Controller.api.bindevent();
    $(document).on("fa.event.appendfieldlist", ".append", function(){
        Form.events.datetimepicker();
    });
},
```

参考链接：[https://ask.fastadmin.net/question/12505.html](https://ask.fastadmin.net/question/12505.html)