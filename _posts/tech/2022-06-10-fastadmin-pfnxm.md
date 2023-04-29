---
layout: post
title: Fastadmin编辑页面 图片相册
category: JavaScript
keywords: 
tags: 常识 JavaScript HTML
description: 
---

edit.html

```
<div class="form-group">
    <label class="control-label col-xs-12 col-sm-2">{:__('Images')}:</label>
    <div class="col-xs-12 col-sm-8">
        <div class="input-group">
            <input id="c-images" class="form-control" size="50" name="row[images]"
              type="text" value="{$row.images}">
            <div class="input-group-addon no-border no-padding">
            <span>
              <button type="button" id="plupload-images" class="btn btn-danger plupload" data-input-id="c-images" 
                data-mimetype="image/gif,image/jpeg,image/png,image/jpg,image/bmp" data-multiple="true" 
                data-preview-id="p-images">
                <i class="fa fa-upload"></i> {:__('Upload')}
              </button>
            </span>
                <span>
              <button type="button" id="fachoose-images" class="btn btn-primary fachoose" data-input-id="c-images" 
                data-mimetype="image/*" data-multiple="true">
                <i class="fa fa-list"></i> {:__('Choose')}
              </button>
            </span>
            </div>
            <span class="msg-box n-right" for="c-images"></span>
        </div>
        <ul class="row list-inline plupload-preview" id="p-images"></ul>
    </div>
</div>
```
edit.js
```
define(['jquery', 'bootstrap', 'backend', 'table', 'form', 'upload'], function ($, undefined, Backend, Table, Form, Upload) {
  // edit定义页面滑动效果
  edit: function () {
    Upload.config.previewtpl = '<li class="col-xs-3"><a href="JavaScript:;" data-url="<%=url%>" class="thumbnail imgswitch">' +
      '<img src="<%=fullurl%>" οnerrοr="this.src=\'' + Fast.api.fixurl("ajax/icon") + 
      '?suffix=<%=suffix%>\';this.οnerrοr=null;" class="img-responsive img-center"></a>' +
      '<a href="javascript:;" class="btn btn-danger btn-xs btn-trash"><i class="fa fa-trash"></i></a></li>';
    $('#p-images').on('click', '.imgswitch',function(){
      let value = $("#c-images").val();
      let data = [];
      value = value.toString().split(",");
      $.each(value, function (index, value) {
        data.push({src: Fast.api.cdnurl(value)});
      });
      Layer.photos({
        photos: {
            "start": $(this).parent().index(),
            "data": data
        },
        anim: 5 //0-6的选择，指定弹出图片动画类型，默认随机（请注意，3.0之前的版本用shift参数）
      });
    });
    Controller.api.bindevent();
  },
}

```