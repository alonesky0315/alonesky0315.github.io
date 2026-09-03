---
layout: post
title: layui 添加wangedit编辑器
category: JavaScript
keywords: Common JavaScript
tags: Common JavaScript
description: 
---

#### layui 添加wangedit编辑器
```
<link href="/static/css/wangeditor.css" rel="stylesheet">
<style>
  #editor—wrapper {
    border: 1px solid #ccc;
    z-index: 100;
    /* 按需定义 */
  }

  #toolbar-container {
    border-bottom: 1px solid #ccc;
  }

  #editor-container {
    height: 500px;
  }
</style>

<div class="layui-form-item">
    <label class="layui-form-label">内容</label>
    <div class="layui-input-block">
      <div id="editor—wrapper">
        <div id="toolbar-container"><!-- 工具栏 --></div>
        <div id="editor-container"><!-- 编辑器 --></div>
      </div>
      <textarea class="layui-hide" id="content"></textarea>
    </div>
</div>

<script src="/static/js/wangeditor.js"></script>

<script >
let content = $('#content').val();
const { createEditor, createToolbar } = window.wangEditor
const editor = createEditor({
    selector: '#editor-container',
    html: content,
    config: {
      placeholder: '请输入内容',
      onChange(html) {
        content = html;
        $('#content').val(content);
      },
      MENU_CONF: {
        uploadImage: {
          fieldName: 'file',
          server: '/travel/upload_image_with_thumb',
          customInsert(res, insertFn) {
            if (res.code !== 200) {
              layer.msg(res.msg || '图片上传失败');
              return;
            }
            // 从 res 中找到 url alt href ，然后插入图片
            insertFn(res.data.url);
          },
        }
      }
    },
    mode: 'default', // or 'simple'
});

const toolbar = createToolbar({
    editor,
    selector: '#toolbar-container',
    config: {},
    mode: 'simple', // or 'simple'
});

// 获取编辑器内容
editor.getHtml()
</script>

```