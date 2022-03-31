---
layout: post
title: Fastadmin基础知识（一）
category: 知识库
keywords: 
tags: 常识 JavaScript HTML 工具
description: 
---

#### js
```javascript   
// 初始化表格
var Controller = {
    index: function () {
        // 初始化表格参数配置
        Table.api.init({
            extend: {
                index_url: 'goods/index',
                add_url: 'goods/add',
                edit_url: 'goods/edit',
                del_url: 'goods/del',
                detail_url: 'addons/detail',
                table: 'mall_goods_goods',
            }
        });
        var table = $("#table");
        table.bootstrapTable({
            url: $.fn.bootstrapTable.defaults.extend.index_url,
            pk: 'id',
            //是否分页
            pagination: false,
            // 排序
            sortName: 'parking_space.id',
            columns: [
                [
                    {checkbox: true},
                    // align 对齐方式
                    {field: 'id', title: __('Id'),align: 'left'},
                    // visible 列中字段是否显示
                    // operate 通用搜索时的搜索条件。一般可使用false,LIKE、RANGE、BETWEEN、FIND_IN_SET、>、<、=等等
                    {field: 'user.realname', title: __('Realname'),visible: false,operate:false},
                    {field: 'photos', title: __('Photos'), operate: false,formatter: Controller.api.formatter.photos},
                    // 模糊搜索
                    {field: 'region_name', title: __('Region_name'),operate: 'LIKE %...%', placeholder: '模糊搜索',
                        process: function (value, arg) {
                        return value.replace(/\*/g, '%');
                    }},
                    // 关联表
                    {field: 'user', title: __('Realname'), formatter:function(user){
                        if(user) {
                            return user.realname;
                        }
                        return '';
                    }},
                    // 开关与searchList搜索列表
                    {
                        field: 'switch', 
                        title: __('Switch'), 
                        align: 'center',
                        searchList: {"1":__('Yes'),"0":__('No')}, 
                        table: table, 
                        formatter: Table.api.formatter.toggle,
                        yes: 'normal',
                        no: 'hidden'
                    },
                    // 自定义链接
                    {
                        field: 'archives.url', title: __('Url'), formatter: function (value, row, index) {
                            return '<a href="' + value + '" target="_blank" class="btn btn-default btn-xs"><i class="fa fa-link"></i></a>';
                        }
                    },
                    // 多个标签
                    {
                        field: 'tags',
                        title: __('tags'),
                        searchList: $.getJSON("product/tag/ajaxList"),
                        align: 'left',
                        formatter: function (value, row, index) {
                            return Table.api.formatter.flag.call(row.tags_text, row.tags_text, row, index);
                        }
                    },
                    // 自定义列表输入修改
                    {
                        field: 'weigh',
                        title: __('Weigh'),
                        formatter: function (value, row, index) {
                            return '<input type="text" class="form-control text-center text-weigh" data-id="' + row.id + '" value="' + value + '" style="width:50px;margin:0 auto;" />';
                        },
                        events: {
                            "dblclick .text-weigh": function (e) {
                                e.preventDefault();
                                e.stopPropagation();
                                return false;
                            }
                        }
                    },
                    {
                        field: 'starttime', 
                        title: __('Starttime'), 
                        operate:'RANGE', 
                        // 老版本 添加样式 日期：datepicker|日期+时间：datetimepicker|时间：timepicker
                        // 新版本 添加样式 datetimerange 格式时间：datetimeFormat:"YYYY-MM-DD",
                        addclass:'datetimepicker', 
                        // 老版本 日期：Table.api.formatter.date|时间：Table.api.formatter.datetime
                        // 新版本 日期加时间 Table.api.formatter.datetime
                        formatter: Table.api.formatter.datetime
                    },
                    // 自定义格式化返回数据
                    {
                        field: 'status', 
                        title: __('Status'), 
                        operate: false, 
                        formatter: function (type) {
                            var types = [__('Type 0'),__('Type 1')];
                            return types[type];
                        }
                    },
                    // 自定义按钮
                    {
                        field: 'operate',
                        title: __('Operate'),
                        table: table, 
                        events: Table.api.events.operate,
                        formatter: Table.api.formatter.operate,
                        // 老版本
                        /*
                        buttons: [
                            {
                                name: 'repairEvaluate',
                                text: __('评价列表'),
                                title: __('评价列表'),
                                classname: 'btn btn-xs btn-success btn-dialog',
                                url: 'service/repair/evaluate',
                            },
                        ]
                        */
                        //新版本
                        buttons: [
                            {
                                name: 'is_verify_1',
                                text: __('通过'),
                                icon: 'fa fa-eye',
                                title: __('审核认证'),
                                confirm: '确认通过吗？',
                                classname: 'btn btn-xs btn-success btn-ajax',
                                extend: "target='_blank'",
                                url: 'parking_space/confirm_verify?is_verify=1',
                                visible: function (row) {
                                    if (row.is_verify == 0) {
                                        return true;
                                    } else {
                                        return false;
                                    }
                                },
                                success: function (data) {
                                    $(".btn-refresh").trigger("click");
                                    //如果需要阻止成功提示，则必须使用return false;
                                    //return false;
                                },
                                error: function (data) {
                                    if(data.code!=0){
                                        layer.msg(data.msg);
                                    }
                                    return false;
                                }
                            }
                        ]
                    }
                ]
            ]
        });
        // 为表格绑定事件
        Table.api.bindevent(table);
        //点击刷新
        $("#common_search").bind("click",function () {
            table.bootstrapTable('refresh');
        });
        // 列表输入修改事件
        $(document).on("change", ".text-weigh", function () {
            $(this).data("params", {weigh: $(this).val()});
            Table.api.multi('', [$(this).data("id")], table, this);
            return false;
        });
        // SelectPage的data-format-item在1.2.0之前的版本只支持使用JS赋值function来实现格式化模板功能，如：
        $("#c-category").data("format-item", function(row){
            return row.title + " - " + row.author;
        });
        // 从FastAdmin1.2.0版本开始，同时还支持占位符和模板，如：
        // 占位符模式
        // <input type="text" ... data-format-item="{title} - {author}" />
        // 模板模式
        // <input type="text" ... data-format-item="#titletpl" />
        // <script type="text/html" id="titletpl">
        // <%=title%> - <%=id%>
        // </script>
        // 温馨提示：
        // 默认由于data-field="name"只能指定一个显示的字段，如果需要调用显示多个字段值时，必须在控制器指定
        // protected $selectpageFields = "id,name,title,author";
    },
    add: function () {
        Controller.api.bindevent();
        // 默认最后一个点击选中
        $("input[name='row[freighttype]']:last").trigger("click");
        // 默认第一个点击选中
        $("input[name='row[integral]']:first").trigger("click");
        Controller.handleCommunityState();
        $("#community_code").change();
    },
    edit: function () {
        Controller.api.bindevent();
        //修改默认选中
        $("input[name='row[integral]']:checked").trigger("fa.event.integralUpdated", "edit");
    },
    api: {
        bindevent: function () {
            //不可见的元素不验证
            $("form[role=form]").data("validator-options", {ignore: ':hidden'});
            $(document).on("click fa.event.integralUpdated", "input[name='row[integral]']", function (e, ref) {
                $(".intp").removeClass("hidden");
                $(".intp.intp-" + $(this).val()).addClass("hidden");
            });
            Form.api.bindevent($("form[role=form]"));
        },
        formatter: {
            //格式化图片
            photos: function (value, row, index) {
                var photosarr,item='';
                photosarr = value.split(",");
                for(var x=0;x<photosarr.length;x++){
                    if ((photosarr[x].indexOf("jpg") > -1)||(photosarr[x].indexOf("png") > -1)) {
                        item+='<a href="' + photosarr[x] + '" target="_blank"><img src="' + photosarr[x] + 
                            '" alt="" style="margin-right:10px;max-height:70px;max-width:70px"></a>';
                    } else {
                        item+='<a href="' + photosarr[x] + '" style="margin-right:10px;" target="_blank">' + 
                        __('None') + '</a>';
                    }
                }
                return item;
            }
        }
    },
    //自定义方法
    handleCommunityState: function (showAll) {
        var building_id = $("#building_id").val();
        $("#community_code").bind("change",function(){
            var community_code = $(this).val();
            $.ajax({
                type: "POST",
                url: 'house/index/get_building_by_cm_code',
                async: true,
                cache: false,
                dataType : "json",
                data: {community_code:community_code},
                success: function(data) {
                    var building = data.building;
                    var buildingHtml = showAll ? '<option value="">全部</option>' : '';
                    $.each(building,function(index,item){
                        buildingHtml += '<option value="'+item.code+'">'+item.name+'</option>';
                    });
                    if(buildingHtml == ''){
                        buildingHtml = '<option value="">没有任何选中项</option>';
                    }
                    $("#building_code").html(buildingHtml);
                    if (building_id) {
                        $("#building_code").val(building_id);
                    }
                    setTimeout(function () {
                        $("#building_code").selectpicker({
                            showTick:true,
                            liveSearch:true
                        });
                        $("#building_code").selectpicker("refresh");
                        $("#building_code").change();
                    },500);
                }
            });
        });
    },
}
```
#### Controller
```php
public function index(){
    //设置过滤方法
    $this->request->filter(['strip_tags']);
    if ($this->request->isAjax()) {
        //如果发送的来源是Selectpage，则转发到Selectpage
        if ($this->request->request('keyField')) {
            return $this->selectpage();
        }
        list($where, $sort, $order, $offset, $limit) = $this->buildparams();
        $total = $this->model
            ->with(['user'=>function($query){
                $query->withField('id, realname');
            },'parking_space'=>function($query){
                $query->withField('id, name');
            }])
            ->where($where)
            ->where(['parking_space.is_delete'=>0])
            ->order($sort, $order)
            ->count();
        $list = $this->model
            ->with(['user'=>function($query){
                $query->withField('id, realname');
            },'parking_space'=>function($query){
                $query->withField('id, name');
            }])
            ->where($where)
            ->where(['parking_space.is_delete'=>0])
            ->order($sort, $order)
            ->limit($offset, $limit)
            ->select();
        $list = collection($list)->toArray();
        foreach ($list as &$item) {
            $item['parking_space.status']=$item['status'];
        }
        //print_r($list);exit;
        $result = array("total" => $total, "rows" => $list);
        return json($result);
    }
    $this->view->assign('breadCrumb',array(sprintf('%s（%s）- %s',$service['name'],$service['id'], '服务项目')));
    return $this->view->fetch();
}
public function detail($ids = null) {
    //设置过滤方法
    $this->request->filter(['strip_tags']);
    if (filter_var($ids,FILTER_VALIDATE_INT)) {
        $append = array(
            array('id','=',$ids)
        );
    } else {
        $append = array(
            array('sid','=',$ids)
        );
    }
    list($where, $sort, $order, $offset, $limit, $orderParams) = $this->buildparams(null,null,$append);
    $service = $this->model
        ->with(['item' =>
            function ($query) {
                $query->order(array('id' => 'desc'));
            }
        ])
        ->where($where)->find();
    if ($this->request->isAjax()) {
        //如果发送的来源是Selectpage，则转发到Selectpage
        if ($this->request->request('name')) {
            return $this->selectpage();
        }
        $result = array("rows" => $service['item']);
        return json($result);
    }
    $this->view->assign('sid',$service['id']);
    $this->view->assign('breadCrumb',array(sprintf('%s（%s）- %s',$service['name'],$service['id'], '服务项目')));
    return $this->view->fetch();
} 
public function add($ids = null) {
    if ($this->request->isPost()) {
        $params = $this->request->post("row/a");
        $params['house']=2;
        $this->request->post(['row' => $params]);
    }
    $this->view->assign('row',array('sid'=>$ids));
    return parent::create();
}
//带参数ids的列表
public function edit($ids = null) {
    if ($this->request->isPost()) {
        $params = $this->request->post("row/a");
        $this->request->post(['row' => $params]);
    }
    return parent::modify($ids,'edit');
}
```  
#### Model
```php
// 追加属性
protected $append = [
    'status_text'
];
//获取器
public function getTypeAttr($value, $data)
{
    $value = $value ? $value : (isset($data['type']) ? $data['type'] : '');
    $list=['0'=>__('Type 0'),'1'=>__('Type 1')];
    return isset($list[$value]) ? $list[$value] : '';
}
//自定义获取器
public function getStatusTextAttr($value, $data)
{
    $value = $value ? $value : (isset($data['type']) ? $data['type'] : '');
    $list=['0'=>__('Type 0'),'1'=>__('Type 1')];
    return isset($list[$value]) ? $list[$value] : '';
}
//设置器
protected function setEndtimeAttr($value)
{
    return $value && !is_numeric($value) ? strtotime($value) : $value;
}

```
#### html
``` html
// 渲染select selectpage
// data-field:name要查询字段，默认name
// data-params:自定义参数 data-params='{"custom[pid]":"0"}' data-params={"custom[pid]":["in","1,3"]}
// data-page-size:分页条数
// data-source:ajax地址--仅适用于下拉
//data-live-search:搜索|data-rule:规则

<input data-params='{"custom[pid]":"0"}' data-rule="required" data-page-size="7" data-source="mall/area/selectpage" 
data-field="sname" data-live-search="true" class="selectpage" />
<select id="building_code" data-rule="required" class="form-control" name="row[building_code]" data-live-search="true"></select>
                   
// 时间 data-date-forma:时间格式|data-use-current:是否使用当前

<input class="datetimepicker" data-date-format="YYYY-MM-DD" data-use-current="true" value="{$row.date|date="Y-m-d H:i:s",###}" />

// 根据字段改变子字段
<div class="form-group">
    <label class="control-label col-xs-12 col-sm-2">{:__('Integral')}:</label>
    <div class="col-xs-12 col-sm-8">
        <div class="radio">
            <label for="row[integral]-0"><input id="row[integral]-0" name="row[integral]" type="radio" value="0" 
            checked /> 否</label>
            <label for="row[integral]-1"><input id="row[integral]-1" name="row[integral]" type="radio" value="1" /> 是</label>
        </div>
    </div>
</div>
<div class="form-group intp intp-0">
    <label class="control-label col-xs-12 col-sm-2">{:__('Integralprice')}:</label>
    <div class="col-xs-12 col-sm-8">
        <input id="c-integralprice" data-rule="required" class="form-control" name="row[integralprice]" type="text" value="">
    </div>
</div>
// 城市下拉
<div class="form-group">
    <label for="community_code" class="control-label col-xs-12 col-sm-2">{:__('Community')}:</label>
    <div class="col-xs-12 col-sm-6">
        <select id="community_code" data-rule="required" class="form-control selectpicker show-tick"
                name="row[community_code]" data-live-search="true">
            {foreach name="community" item="vo"}
            <option value="{$vo.code}" {in name="vo.code" value="$row.community_code"}selected{/in}>{$vo.name}</option>
            {/foreach}
        </select>
    </div>
</div>
<div class="form-group">
    <label for="building_code" class="control-label col-xs-12 col-sm-2">{:__('Building')}:</label>
    <div class="col-xs-12 col-sm-6">
        <select id="building_code" data-rule="required" class="form-control" name="row[building_code]"
                data-live-search="true"></select>
    </div>
</div>
<input type="hidden" name="building_id" id="building_id" value="{$row.building_code}" />

// 自定义表头
{:build_header($breadCrumb)}

// 自定义工具栏
<div id="toolbar" class="toolbar">
    {:build_toolbar('add,edit,del,refresh')}
</div>

// 渲染单选按钮
<div class="form-group">
    <label class="control-label col-xs-12 col-sm-2">{:__('Paystatus')}:</label>
    <div class="col-xs-12 col-sm-6">
        {:build_radios('row[pay_status]', ['0'=>__('Paystatus 0'),'1'=>__('Paystatus 1')],$row['pay_status'])}
    </div>
</div>
// 渲染多选按钮
<div class="form-group">
    <label for="c-editor" class="control-label col-xs-12 col-sm-2">{:__('Others')}:</label>
    <div class="col-xs-12 col-sm-8">
        {:build_multi_checkboxs([['name'=>'row[is_recommend]','list'=>['1'=>__('Recommend')]]])}
    </div>
</div>
```
后续不断更新中......