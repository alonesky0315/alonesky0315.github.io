---
layout: post
title: FastAdmin自定义按钮
category: PHP
keywords: Common JavaScript PHP HTML
tags: Common JavaScript PHP HTML
description: 
---

#### FastAdmin自定义按钮

1. 列表自定义按钮
```javacript
{
    field: 'rooms', title: __('Rooms'), table: table, events: Table.api.events.operate,
    buttons: [
		{
			name: 'room', 
			text: function(row){
					return '(' + row.rooms + ')';
			}, 
			title: __('Rooms'),
			icon: 'fa fa-eye', 
			classname: 'btn btn-xs btn-success btn-dialog',
			url: 'hotel/room/index?hotel_id={ids}',
		}
    ],
    formatter: Table.api.formatter.buttons
}
```

2. 详情JavaScript
```javacript
Table.api.init({
    extend: {
        index_url: 'hotel/room/index' + location.search,
        add_url: 'hotel/room/add' + location.search,
        edit_url: 'hotel/room/edit',
        del_url: 'hotel/room/del',
        multi_url: 'hotel/room/multi',
        import_url: 'hotel/room/import',
        table: 'room',
    }
});
```

3. 控制器
```php
public function index($hotel_id = null)
{
		//设置过滤方法
		$this->request->filter(['strip_tags', 'trim']);
		$this->view->assign('hotel_id', $hotel_id);
		if (false === $this->request->isAjax()) {
			return $this->view->fetch();
		}
		//如果发送的来源是 Selectpage，则转发到 Selectpage
		if ($this->request->request('keyField')) {
			return $this->selectpage();
		}
		[$where, $sort, $order, $offset, $limit] = $this->buildparams();
		$list = $this->model
			->where($where)
			->where('hotel_id', $hotel_id)
			->order($sort, $order)
			->paginate($limit);
		$result = ['total' => $list->total(), 'rows' => $list->items()];
		return json($result);
}
/**
    * 添加
    *
    * @return string
    * @throws \think\Exception
    */
public function add($hotel_id = null)
{
		if (false === $this->request->isPost()) {
			$this->view->assign('hotel_id', $hotel_id);
			return $this->view->fetch();
		}
		$params = $this->request->post('row/a');
		if (empty($params)) {
			$this->error(__('Parameter %s can not be empty', ''));
		}
		$params = $this->preExcludeFields($params);

		if ($this->dataLimit && $this->dataLimitFieldAutoFill) {
			$params[$this->dataLimitField] = $this->auth->id;
		}
		$result = false;
		Db::startTrans();
		try {
			//是否采用模型验证
			if ($this->modelValidate) {
				$name = str_replace("\\model\\", "\\validate\\", get_class($this->model));
				$validate = is_bool($this->modelValidate) ? ($this->modelSceneValidate ? $name . '.add' : $name) : $this->modelValidate;
				$this->model->validateFailException()->validate($validate);
			}
			$result = $this->model->allowField(true)->save($params);
			Db::commit();
		} catch (ValidateException | PDOException | Exception $e) {
			Db::rollback();
			$this->error($e->getMessage());
		}
		if ($result === false) {
			$this->error(__('No rows were inserted'));
		}
		$this->success();
}
```

4. Add页面
```html
<input name="row[hotel_id]" type="hidden" value="{$hotel_id}">
```