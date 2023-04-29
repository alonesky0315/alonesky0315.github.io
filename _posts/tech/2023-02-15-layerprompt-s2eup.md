---
layout: post
title: layer.prompt弹层
category: JavaScript
keywords: 
tags: 常识 JavaScript
description: 
---

使用layer.open实现方式：

```
layer.open(
	{
		//formType: 2,// 这里依然指定类型是多行文本框，但是在下面content中也可绑定多行文本框在这里插入代码片
		title: '是否确认删除?',
		area: ['300px', '240px'],
		btnAlign: 'c',
		closeBtn:'1',// 右上角的关闭
		content: `<div><p>备注:</p><textarea name="txt_remark" id="remark" style="width:200px;height:80px;"></textarea></div>`,
		btn:['确认','取消','关闭'],
		yes: function (index, layero) {
		var remark = $('#remark').val();// 获取多行文本框的值
		console.log('您刚才输入了:' + remark);
		layer.close(index);
		// 可执行确定按钮事件并把备注信息（即多行文本框值）存入需要的地方
	},
	no:function(index){
		console.log('您刚才点击了取消按钮');
		layer.close(index);
		return false;// 点击按钮按钮不想让弹层关闭就返回false
	},
	close:function(index){
		console.log('您刚才点击了关闭按钮');
		return false;// 点击按钮按钮不想让弹层关闭就返回false
	}
});

```

附：layer.open中按钮点击事件：

```
layer.open({
	content: 'test',
	btn: ['按钮一', '按钮二', '按钮三'],
	yes: function(index, layero){
		// 按钮【按钮一】的回调
	},
	btn2: function(index, layero){
		// 按钮【按钮二】的回调
		console.log('这是点击了按钮二');
		return false;// 点击后不关闭窗口，需返回false
	},
	btn3: function(index, layero){
		// 按钮【按钮三】的回调
		console.log('这是点击了按钮三');
		return false;// 点击后不关闭窗口，需返回false
	},
	cancel: function(){
		// 右上角关闭回调
	}
});

```

用layer.open可以实现弹出文本框及按钮事件,layer.prompt也是在layer.open基础上的封装…

使用layer.prompt时,如果文本框输入值是空的话点击确定没有反应,不能向下执行。但是我又需要在这种情况下去继续执行判断或逻辑时该怎么做？

注：使用layer.prompt时，layer使用的版本为layer-v3.0

官方代码说明如下：

prompt层新定制的成员如下

```
{
	formType: 1, // 输入框类型，支持0（文本）默认1（密码）2（多行文本）
	value: '', // 初始时的值，默认空字符
	maxlength: 140, // 可输入文本的最大长度，默认500
}
```

例子1
```
layer.prompt(function(value, index, elem){
	console.log(value); // 得到value
	layer.close(index);
});
```

例子2
```
layer.prompt({
		formType: 2,
		value: '初始值',
		title: '请输入值',
		btnAlign: 'c',
		area: ['800px', '350px'] // 自定义文本域宽高
	}, function(value, index, elem){
		console.log(value); // 得到value
		layer.close(index);
	});
```

由官网查看文档可知layer.prompt也是继承layer.open的，所以改成如下所示：

```
layer.prompt({
    formType: 2,
    title: '请填写排除原因（注：必填项）',
    area: ['500px', '150px'],
    btnAlign: 'c',
    yes: function(index, layero){
        // 获取文本框输入的值
        var value = layero.find(".layui-layer-input").val();
        if (value) {
            console.log("输入值为：" + value);
            layer.close(index);
        } else {
            console.log("输入值为空！");
        }
    }
});
```

说明：以上弹出的文本框是layer自带的，根据formType指定的，如果自己绑定文本框则可如下尝试：
```
layer.prompt({
	formType: 2,// 这里依然指定类型是多行文本框，但是在下面content中也可绑定多行文本框
	title: '是否认删除?',
	area: ['300px', '100px'],
	btnAlign: 'c',
	content: `<div><p>备注:</p><textarea name="txt_remark" id="remark" style="width:200px;height:80px;"></textarea></div>`,
	yes: function (index, layero) {
		var remark = $('#remark').val();// 获取多行文本框的值
		console.log('您刚才输入了:' + remark);
		// 可执行确定按钮事件并把备注信息（即多行文本框值）存入需要的地方
    }
});
```


原文链接：[https://blog.csdn.net/cheers_bin/article/details/110478253](https://blog.csdn.net/cheers_bin/article/details/110478253)