---
layout: post
title: Fastadmin表格tab标签 全面解析
category: 知识库
keywords: 
tags: 常识
description: 
---

一、如何生成一个选项卡

1. 框架 为status(枚举)字段自动生成 tab选项卡

2. 自定一个选项卡

```
#参考数据表设计如下

create table fa_good(
id int(10) auto_increment comment 'id'primary key, 
   name varchar(50) not null comment '名字',
   weight int(5) not null comment '重量',
   image text not null comment '商品主图',
   category_id int(10) not null comment '类别',
   createtime int(10) not null comment '发布时间',
   updatetime int(10) not null comment '更新时间',
   status tinyint default 0 null commet '1|已上架,2|未上架'
) comment '商品';
```

①js中 goods.js

选项卡是通过动态改变通用搜索 的值来实现的 所以表格的配置项commonSearch不能为false

同时该列的配置项 searchable 不能为false

```
columns: [
    [
        {checkbox: true},
        {field: 'id', title: __('Id')},
        {field: 'name', title: __('Name')},
        {field: 'weight', title: __('Weight')},
        {field: 'category.name', searchList:{"粮食":"粮食","调料":"调料","油类":"油类"},title: __('类别')}, 
        //不要显示在表格中 可以设置 visible:false
        //默认值的情况 使用 defaultValue:'粮食'
        //searchList 中的json数据可以是静态的 
        //或者ajax获取 $.getJSON('text/goods/searchlist?getcategoryList')
        //或者通过控制器传递变量 $this->assignconfig("categoryList",model("Category")->getCategoryList()); js中 Config.categoryList调用
        {field: 'status', title: __('状态')},
        {field: 'createtime', title: __('发布时间'),formatter: Table.api.formatter.datetime},
        {field: 'operate', title: __('Operate'), table: table, events: Table.api.events.operate, formatter: Table.api.formatter.operate}
    ]
]
```

②模板中 view/goods/index

```
<div class="panel-heading">
{:build_heading(null,FALSE)} //不要构建头部
    <ul class="nav nav-tabs" data-field="category.name"> <!--data-field 是点击tab选卡后 在对应js中搜索{field: 'category.name'，searchList:{"粮食":"粮食","调料":"调料","油类":"油类"} 中 searchList对应的值}-->
    <li class="active"><a href="#t-all" data-value="" data-toggle="tab">{:__('All')}</a></li>//设置第一个 全部 选项卡
        <!--便利控制器 传递到模板中的变量 这个值可以是静态的 也可以是由 模型的获取器来动态获取例如 statusList-->
    <!--静态的情况-->
        <li><a href="#t-粮食" data-value="粮食" data-toggle="tab">粮食</a></li>
        <li><a href="#t-油类" data-value="油类" data-toggle="tab">油类</a></li>
        <!--由模型维护的情况-->
        <!--在控制器中传递参数到模板页        $this->assign("categoryList",model("Category")->getCategoryList()); -->
        {foreach name="categroyList" item="vo"}
        <li><a href="#t-{$key}" data-value="{$key}" data-toggle="tab">{$vo}</a></li>
        <!--data-value 是对应searchList的json的key-->
        {/foreach}
    </ul>
</div>

<!--如果要默认不是全部的话 在需要默认的tab上使用 <li class="active">-->
```

③控制器 controller/goods.php

* 在控制中合适的时期传递参数

```
public function _initialize(){
    parent::_initialize();
    $this->model = new \app\admin\model\test\Good;
    //框架生成的status是这样做的
    $this->view->assign("statusList", $this->model->getStatusList());
    //对应上面的说的方法 在Config中给js传递变量
    $this->assignconfig("categoryList",model("Category")->getCategoryList());
    //给模板页面传递变量
    $this->assign("categoryList",model("Category")->getCategoryList());
}

//当然也可以在index方法取得数据后传递 这里自由发挥
```

④模型中 model/goods.php

* 在模型中要可以通过 获取器 维护筛选字段的数据
* 利用模型维护并不是必须的

```
// 追加属性
protected $append = [
    'status_text',
];

//这是框架生成status 组织statuslist的值
public function getStatusList(){
    return ['0' => __('Status 0'), '1' => __('Status 1')];
}
//获取器维护追加字段 `status_text`的值
public function getStatusTextAttr($value, $data){
    $value = $value ? $value : (isset($data['status']) ? $data['status'] : '');
    $list = $this->getStatusList();
    return isset($list[$value]) ? $list[$value] : '';
}
```

二、示例1——利用tab选项卡 实现按时间段筛选

通过选项卡 筛选今日 本周 本月 本年度 新增的商品

1. 在模型中维护这个筛选列表

```
public function getDateFilter(){
   return array('today'=>'今天','week'=>'本周','month'=>'本月','year'=>'今年');
}
```

2. 在控制器中传值给 js 和 模板 并实现筛选

```
public function _initialize(){
    parent::_initialize();
    $this->model = new \app\admin\model\test\Good;
    $dateFilter=$this->model->getDateFilter();
    $this->assign("datefilter",$dateFilter);
    $this->assignconfig("datefilter",$dateFilter);
}
public function index(){
    //当前是否为关联查询
    $this->relationSearch = true;
    //设置过滤方法
    $this->request->filter(['strip_tags', 'trim']);
    if ($this->request->isAjax()){
        //获取filter 这里的情况是 由于筛选提交的值与字段值冲突 都为createtime 
        //这里将filter 拆解开 重新组装
        $filter = $this->request->get("filter", '');
        $op=$this->request->get("op", '', 'trim');
        $filter = (array)json_decode($filter, true);
        $op = (array)json_decode($op, true);
        $dateFilter="";//将筛选的值放在这里
        if(isset($filter['createtime'])){
            $dateFilter=$filter['createtime'];
            unset($filter['createtime']);
            unset($op['createtime']);
            //去掉了createtime 的filter 重新组织到 request中 交给下面 buildparams()方法去构建query 的条件
            Request::instance()->get(['filter'=>json_encode($filter)]);
            Request::instance()->get(['op'=>json_encode($op)]);
        }
        //如果和字段不冲突就玩去不需要 折腾request 见示例2
        list($where, $sort, $order, $offset, $limit) = $this->buildparams();
        $total = $this->model
            ->with(['category'])
            ->where($where)
            ->whereTime("createtime",$dateFilter) //tp5的 时间区间 假设这里 变量的值为 week 最后执行的sql WHERE  `createtime` BETWEEN 1593532800 AND 1596211200
            ->order($sort, $order)
            ->count();

        $list = $this->model
            ->with(['category'])
            ->where($where)
            ->order($sort, $order)
            ->whereTime("createtime",$dateFilter)
            ->limit($offset, $limit)
            ->select();
        foreach ($list as $row){
            $row->image=Config::get('url_domain_root').$row['image'];
        }
        $list=collection($list)->toArray();
        $result = array("total" => $total, "rows" => $list);
        return json($result);
    }
    return $this->view->fetch();
}
```

3. 在js中设置searchList

```
columns: [
    [
        {checkbox: true},
        {field: 'id', title: __('Id')},
        {field: 'name', title: __('Name')},
        {field: 'weight', title: __('Weight')},
        {field: 'status', title: __('状态')},
        {field: 'createtime', title: __('发布时间'),searchList:Config.datefilter,//调用assignconfig传递的变量
         formatter: Table.api.formatter.datetime},
        {field: 'operate', title: __('Operate'), table: table, events: Table.api.events.operate, formatter: Table.api.formatter.operate}
    ]
]  
```

4. 在模板页面

```
<div class="panel-heading">
    {:build_heading(null,FALSE)}
    <ul class="nav nav-tabs" data-field="createtime">
        <li class="active"><a href="#t-all" data-value="" data-toggle="tab">{:__('All')}</a></li>
        {foreach name="datefilter" item="vo"}
        <li><a href="#t-{$key}" data-value="{$key}" data-toggle="tab">{$vo}</a></li>
        {/foreach}
    </ul>
</div>
```

三、示例2——tab选项卡 多个参数筛选 玩坏它

通过选项卡 筛选 今天-未上架 今天-已上架 本周-未上架 本周-已上架 本月-未上架 本月-已上架 联合日期范围与状态

1. 在模型中维护这个筛选列表

```
public function getStatusList(){
    return array("1"=>"已上架","0"=>"未上架");
}
public function getDateFilter(){
    return array('today'=>'今天','week'=>'本周','month'=>'本月');
}
```

2. 在控制器中传值给 js 和 模板 并实现筛选

```
public function _initialize(){
    parent::_initialize();
    $this->model = new \app\admin\model\test\Good;
    $dateFilter=$this->model->getDateFilter();
    $statusFilter=$this->model->getStatusList();
    $this->assign("datefilter",$dateFilter);
    $this->assign("statuslist",$statusFilter);
    $this->assignconfig("datefilter",$dateFilter);
    $this->assignconfig("statuslist",$statusFilter);
}
public function index(){
    //当前是否为关联查询
    $this->relationSearch = true;
    //设置过滤方法
    $this->request->filter(['strip_tags', 'trim']);
    if ($this->request->isAjax()){
        $cusFilter = $this->request->get("cusfilter", '');//获取传递的参数
        $cusFilter=(array)json_decode($cusFilter,true);
        list($where, $sort, $order, $offset, $limit) = $this->buildparams();
        if(isset($cusFilter['statusfilter'])) {//如果参数不是空的 增加query的条件
            $this->model->where('status', $cusFilter['statusfilter'])
                ->whereTime("createtime",$cusFilter['datefilter']);
        }
        $total = $this->model
                ->with(['category'])
                ->where($where)
                ->order($sort, $order)
                ->count();
        if(isset($cusFilter['statusfilter'])) {
            $this->model->where('status', $cusFilter['statusfilter'])
            ->whereTime("createtime",$cusFilter['datefilter']);
        }
        $list = $this->model
            ->with(['category'])
            ->where($where)
            ->order($sort, $order)
            ->limit($offset, $limit)
            ->select();
        foreach ($list as $row){
            $row->image=Config::get('url_domain_root').$row['image'];
        }
        $list=collection($list)->toArray();
        $result = array("total" => $total, "rows" => $list);
        return json($result);
    }
    return $this->view->fetch();
}
```

3. 在js中设置searchList

```
//这里要自己实现tab的事件 就不需要设置searchList
columns: [
     [
         {checkbox: true},
         {field: 'id', title: __('Id')},
         {field: 'name', title: __('Name')},
         {field: 'weight', title: __('Weight')},
         {field: 'status', title: __('状态')},
         {field: 'createtime', title: __('发布时间'),formatter: Table.api.formatter.datetime},
     ]
 ]

// 为表格绑定事件
Table.api.bindevent(table);
//为TAB绑定事件
$('.panel-heading a[data-toggle="tab"]').on('shown.bs.tab',function (e) {
    let tab=$(this);
    let options=table.bootstrapTable("getOptions");
    options.pageNumber=1;
    options.queryParams=function (params) {
        let cusfilter={};
        cusfilter['datefilter']=tab.data("datefilter")
        cusfilter['statusfilter']=tab.data("statusfilter")
        params.cusfilter=JSON.stringify(cusfilter);
        return params;
    }
    table.bootstrapTable('refresh',{})//刷新表格
    return false
})
```

4. 在模板页面

```
<div class="panel-heading">
    {:build_heading(null,FALSE)}
    <ul class="nav nav-tabs"> <!-- data-filed 就没有意义了-->
        <li class="active"><a href="#t-all" data-toggle="tab">{:__('All')}</a></li>
        {foreach name="datefilter" item="vo" key="i"}
            {foreach name="statuslist" item="row" key="j"}
                   <li><a href="#t-{$vo}-{$row}" data-datefilter="{$i}" data-statusfilter="{$j}" data-toggle="tab">{$vo}-{$row}</a></li>
                <!--这里对应的删除data-value 设置了data-datefilter data-statusfilter-->
            {/foreach}
        {/foreach}
    </ul>
</div>
```

原文链接：[https://ask.fastadmin.net/article/21374.html](https://ask.fastadmin.net/article/21374.html)