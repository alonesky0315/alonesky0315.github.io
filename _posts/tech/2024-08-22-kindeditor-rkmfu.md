---
layout: post
title: KindEditor 老版本不能赋值提交问题
category: JavaScript
keywords: Common JavaScript
tags: Common JavaScript
description: 
---

> KindEditor 老版本不能赋值提交问题

```
<script type="text/javascript">
	var editor;
	KindEditor.ready(function(K) {
		editor = K.create('textarea[name="content"]', {
			allowFileManager: true,
			resizeType: 1,
			width: '100%',
			afterBlur: function() {
				this.sync();
			}
		});
	});
</script>
```