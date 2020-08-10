---
layout: post
title: fastadmin基础教程（二）
category: 技术
keywords: fastadmin,基础教程
tags: 技术,fastadmin,基础教程
description: fastadmin基础教程（二）
---

- 自定义按钮（老版）
``` js
{
    field: 'refundMontyis',
    title: __('退款'),
    table: table,
    events: Controller.api.events.buttons,
    buttons: [
        {
            name: 'confirm_refund_money',
            text: __('退款'),
            title: __('退款'),
            classname: 'btn btn-xs btn-success btn-refund btn-ajax',
            extend:'data-confirm="确认退款吗？"'
        }
    ],
    formatter: Controller.api.formatter.buttons,
},
api: {
    events:{
        buttons: {
            'click .btn-refund': function (e, value, row, index) {
                e.stopPropagation();
                var that = this;
                var top = $(that).offset().top - $(window).scrollTop();
                var left = $(that).offset().left - $(window).scrollLeft() - 260;
                if (top + 154 > $(window).height()) {
                    top = top - 154;
                }
                if ($(window).width() < 480) {
                    top = left = undefined;
                }
                var index = Layer.confirm(
                    $(that).data('confirm'),
                    {icon: 3, title: __('Warning'), offset: [top, left], shadeClose: true},
                    function (index) {
                        var table = $(that).closest('table');
                        var options = table.bootstrapTable('getOptions');
                        var params = {
                            url: 'mall/order/refund/orderRefund',
                            data: {
                                ids: row[options.pk],
                            }
                        };
                        Fast.api.ajax(params, function (data) {
                            table.bootstrapTable('refresh');
                        });
                        Layer.close(index);
                    }
                );
            }
        }
    },
    formatter:{
        buttons: function (value, row, index) {
            var table = this.table;
            // 操作配置
            var options = table ? table.bootstrapTable('getOptions') : {};
            // 默认按钮组
            var buttons = $.extend([], this.buttons || []);
            var html = [];
            var url, classname, icon, text, title, extend;
            //判断按钮是否显示
            if(row.refundStatus==1){
                $.each(buttons, function (i, j) {
                    var attr = table.data("buttons-" + j.name);
                    if (typeof attr === 'undefined' || attr) {
                        url = j.url ? j.url : '';
                        if (url.indexOf("{ids}") === -1) {
                            url = url ? url + (url.match(/(\?|&)+/) ? "&ids=" : "/ids/") + row[options.pk] : '';
                        }
                        url = Table.api.replaceurl(url, value, row, table);
                        url = url ? Fast.api.fixurl(url) : 'javascript:;';
                        classname = j.classname ? j.classname : 'btn-primary btn-' + name + 'one';
                        icon = j.icon ? j.icon : '';
                        text = j.text ? j.text : '';
                        title = j.title ? j.title : text;
                        extend = j.extend ? j.extend : '';
                        html.push('<a href="' + url + '" class="' + classname + '" ' + extend + ' title="' + title
                            + '"><i class="' + icon + '"></i>' + (text ? ' ' + text : '') + '</a>');
                    }
                });
            }
            return html.join(' ');
        },
    }
}
```    
- 追加搜索条件  
``` php
$this->request->filter(['strip_tags']);
if (filter_var($ids, FILTER_VALIDATE_INT)) {
    $append =[['fid'=>$ids]];
} else {
    $append =[['id'=>$ids]];
}
list($where, $sort, $order, $offset, $limit, $orderParams) = $this->buildparams(null, null, $append);
[filter_var PHP 过滤器](https://www.runoob.com/php/php-ref-filter.html)  
filter_var() 函数通过指定的过滤器过滤一个变量
```
- 公共搜索   
```html   
{include file="expenses/index/common_search" /}
<div class="commonsearch-table">
    <form class="form-horizontal form-commonsearch nice-validator n-default" role="form" action="" novalidate="novalidate">
        <fieldset>
            <div class="row">
                <div class="form-group col-xs-12 col-sm-6 col-md-4 col-lg-3">
                    <label for="community_code" class="control-label col-xs-4">{:__('Community')}</label>
                    <div class="col-xs-12 col-sm-8">
                        <select id="community_code" class="form-control selectpicker show-tick" name="query[community_code]" data-live-search="true">
                            <option value="" {in name="query.community_code" value=""}selected{/in}>全部</option>
                            {foreach name="community" item="vo"}
                            <option value="{$vo.code}">{$vo.name}</option>
                            {/foreach}
                        </select>
                    </div>
                </div>
                <div class="form-group col-xs-12 col-sm-6 col-md-4 col-lg-3">
                    <div class="col-sm-8 col-xs-offset-4">
                        <button type="button" class="btn btn-primary" id="common_search">查询</button>
                        <button type="reset" class="btn btn-default">重置</button>
                    </div>
                </div>
            </div>
        </fieldset>
    </form>
</div>
### js 附件：图一、图二
table.bootstrapTable({
    url: $.fn.bootstrapTable.defaults.extend.index_url,
    escape: false, //无限分类字符乱码
    pk: 'id',
    sortName: 'weigh,id',
    sortOrder:'asc,desc',
    //sortName: 'weigh desc,id asc', sortOrder失效
    pagination: true,//是否分页
    pageSize: 10,//每页条数
    commonSearch: false,//是否启用公共搜索
    search:false,//去除右侧搜索框，移除或true为打开
    searchFormVisible:true,//默认显示table搜索框 false 点击显示
    showToggle:false, //去除切换功能，移除或true为打开
    showColumns:false, //去除列功能，移除或true为打开
    showExport:false, //去除导出数据功能，移除或true为打开
    showJumpto:false, //分页插件，实现自定义跳转页面 [showJumpto](https://ask.fastadmin.net/article/12294.html)  
    pageList: [10, 20, 50, 'All'],//每页条数
    //重组排序字段
    queryParams: function queryParams(params) {
        var searchForm = $("form[role=form]");
        if(searchForm.length){
            var searchFields = searchForm.serializeArray();
            for(var i=0;i<searchFields.length;i++) {
                if(searchFields[i]['value']) {
                    params[searchFields[i]['name']] = searchFields[i]['value'];
                }
            }
        }
        return params;
    },
    queryParamsType : "limit",
    columns: [
        [
            {checkbox: true},
            //sortable 添加排序
            {field: 'id', title: __('Id'),sortable:true},
            //筛选
            {field: 'goods_id', title: __('Goods_id'), operate: "in", formatter: Table.api.formatter.search},
            {field: 'project', title: __('Project'), formatter: function (project) {
                if(project) {
                    return project.name;
                }
                return '-';
            }},
            {field: 'status', title: __('Status'), operate: false,formatter: function (status) {
                    var types = [__('Status 0'),__('Status 1'),__('Status 2')];
                    return types[status];
            }}
        ]
    ]
});

// 为表格绑定事件 附件：图片三
Table.api.bindevent(table);
//点击刷新
$("#common_search").bind("click",function () {
    table.bootstrapTable('refresh',{
        url: $.fn.bootstrapTable.defaults.extend.index_url,
        pageNumber: 1
    });
});
Controller.api.bindevent();
```   
- 树节点 附件：图四
```js
define(['jquery', 'bootstrap', 'backend', 'table', 'form', 'jstree'], function ($, undefined, Backend, Table, Form, undefined) {
    //读取选中的条目
    $.jstree.core.prototype.get_all_checked = function (full) {
        var obj = this.get_selected(), i, j;
        for (i = 0, j = obj.length; i < j; i++) {
            obj = obj.concat(this.get_node(obj[i]).parents);
        }
        obj = $.grep(obj, function (v, i, a) {
            return v != '#';
        });
        obj = obj.filter(function (itm, i, a) {
            return i == a.indexOf(itm);
        });
        return full ? $.map(obj, $.proxy(function (i) {
            return this.get_node(i);
        }, this)) : obj;
    };
});
api: {
    bindevent: function () {
        Form.api.bindevent($("form[role=form]"), null, null, function () {
            if ($("#treeview").size() > 0) {
                var r = $("#treeview").jstree("get_all_checked");
                $("input[name='row[rules]']").val(r.join(','));
            }
            return true;
        });
        //渲染权限节点树
        //销毁已有的节点树
        $("#treeview").jstree("destroy");
        Controller.api.rendertree(nodeData);
        //全选和展开
        $(document).on("click", "#checkall", function () {
            $("#treeview").jstree($(this).prop("checked") ? "check_all" : "uncheck_all");
        });
        $(document).on("click", "#expandall", function () {
            $("#treeview").jstree($(this).prop("checked") ? "open_all" : "close_all");
        });
    },
    rendertree: function (content) {
        $("#treeview").on('redraw.jstree', function (e) {
            $(".layer-footer").attr("domrefresh", Math.random());
        }).jstree({
            "themes": {"stripes": true},
            "checkbox": {
                "keep_selected_style": false,
            },
            "types": {
                "root": {
                    "icon": "fa fa-folder-open",
                },
                "menu": {
                    "icon": "fa fa-folder-open",
                },
                "file": {
                    "icon": "fa fa-file-o",
                }
            },
            "plugins": ["checkbox", "types"],
            "core": {
                'check_callback': true,
                "data": content
            }
        });
    }
}
> html
<div class="form-group">
    <label class="control-label col-xs-12 col-sm-2">{:__('Permission')}:</label>
    <div class="col-xs-12 col-sm-8">
        <span class="text-muted"><input type="checkbox" name="" id="checkall" /> <label for="checkall"><small>{:__('Check all')}</small></label></span>
        <span class="text-muted"><input type="checkbox" name="" id="expandall" /> <label for="expandall"><small>{:__('Expand all')}</small></label></span>
        <div id="treeview"></div>
    </div>
</div>
<script>
    var nodeData = {:json_encode($nodeList); };
</script>
> php
add.php
$nodeList = \app\admin\model\UserRule::getTreeList();
$this->assign("nodeList", $nodeList);
edit.php
$row = $this->model->get($ids);
if (!$row){
    $this->error(__('No Results were found'));
}
$rules = explode(',', $row['rules']);
$nodeList = \app\admin\model\UserRule::getTreeList($rules);
$this->assign("nodeList", $nodeList);
model
public static function getTreeList($selected = []){
    $ruleList = collection(self::where('status', 'normal')->order('weigh desc,id desc')->select())->toArray();
    $nodeList = [];
    Tree::instance()->init($ruleList);
    $ruleList = Tree::instance()->getTreeList(Tree::instance()->getTreeArray(0), 'name');
    $hasChildrens = [];
    foreach ($ruleList as $k => $v)
    {
        if ($v['haschild'])
            $hasChildrens[] = $v['id'];
    }
    foreach ($ruleList as $k => $v) {
        $state = array('selected' => in_array($v['id'], $selected) && !in_array($v['id'], $hasChildrens));
        $nodeList[] = array('id' => $v['id'], 'parent' => $v['pid'] ? $v['pid'] : '#', 'text' => __($v['title']), 'type' => 'menu', 'state' => $state);
    }
    return $nodeList;
}
```
- 移除排序   
```js
// 初始化表格参数配置
Table.api.init({
    extend: {
        index_url: 'mall/order/refund/index',
        add_url: 'mall/order/refund/add',
        edit_url: 'mall/order/refund/edit',
        del_url: 'mall/order/refund/del',
        multi_url: 'mall/order/refund/multi',
        dragsort_url: '',//移除排序
        table: 'mall_order_refund',
        //设置不同操作的弹窗宽高
        area: {
            add:['800px','350px'],
            edit:['800px','350px']
        }
    }
});
```
- 上传图片绑定事件
```js
// 给上传按钮添加上传成功事件
    $("#plupload-avatar").data("upload-success", function (data) {
        var url = Backend.api.cdnurl(data.url);
        $(".profile-user-img").prop("src", url);
        Toastr.success("上传成功！");
    });
    
    // 给表单绑定事件
    Form.api.bindevent($("#update-form"), function () {
        var url = Backend.api.cdnurl($("#c-avatar").val());
        top.window.$(".user-panel .image img,.user-menu > a > img,.user-header > img").prop("src", url);
        return true;
    });
```
- 双击事件 
```js
//双击重新加载页面
$(document).on("dblclick", ".sidebar-menu li > a", function (e) {
    $("#con_" + $(this).attr("addtabs") + " iframe").attr('src', function (i, val) {
        return val;
    });
    e.stopPropagation();
});
```
- 无刷新 加载 更新 附件：图五
```js
defind 添加 'editable'
var table = $("#table");

//设置字段
var columns = [
    {checkbox: true},
    {field: 'id', title: __('ID')},
    {field: 'goods_id', title: __('Goods_id'), operate: "in", formatter: Table.api.formatter.search, visible:false},
];
//动态追加分类规格作为字段
$.each(Config.specs, function (i, j) {
    var data = {field: "specid_" + i, title: j.specname, operate: 'like'};
    columns.push(data);
});
//追加表后续字段
columns.push({field: 'specitem_ids', title: __('Specitem_ids'), visible:false});
columns.push({field: 'marketprice', title: __('Marketprice'), operate:'BETWEEN', editable: true});
// 初始化表格
table.bootstrapTable({
    url: $.fn.bootstrapTable.defaults.extend.index_url,
    pk: 'id',
    sortName: 'id',
    sortOrder: 'asc',
    columns: columns,
    //pagination: false,
});
// 为表格绑定事件
Table.api.bindevent(table);
```

![图一](https://ae01.alicdn.com/kf/H4adf7bfba1254f1bb3a16d087048a854K.jpg)     
![图二](https://ae01.alicdn.com/kf/H8b034a2d6f93484188dfc52dc21fd4c7I.jpg)    
![图三](https://ae01.alicdn.com/kf/H85fb162c65214bcb93154bc79a4f5382s.jpg)    
![图四](https://ae01.alicdn.com/kf/H0a84e5e510804887a68f36176fed66c9j.jpg)    
![图五](https://ae01.alicdn.com/kf/Hbe99cecbc6bb4a1ab08fabf9172b3f80V.jpg)    

