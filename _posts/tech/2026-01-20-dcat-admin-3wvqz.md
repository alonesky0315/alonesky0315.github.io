---
layout: post
title: Dcat Admin知识库五(杂项)
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

Dcat Admin知识库五(杂项)

五、其他
1. MySQL8.1 JSON数据字段求和
```
// json数据格式
// {\"breakage\": 0, \"discount\": \"1\", \"total_price\": \"165.00\", \"payment_price\": \"165.00\", \"discount_price\": \"0.00\", \"container_price\": 0, \"warehouse_price\": \"0.00\"}
SUM(JSON_EXTRACT(price, \'$.payment_price\') + 0) as sales_volume
```
2. 头部添加弹窗
```
// app/Admin/bootstrap.php
Admin::navbar(function (Navbar $navbar) {
$modal = Modal::make()
	->title('通知列表')
	->lg()
	->body(admin_url('SystemNotices'))
	->button('<i class="fa fa-bullhorn"></i>');
// 将弹窗添加到导航栏右侧（规范渲染，自动绑定事件）
$navbar->right($modal); // 无需手动调用 render()，直接传入对象即可
Admin::script(<<<JS
	// 确保 DOM 完全加载后绑定事件
	$(document).ready(function() {
		// 绑定唯一 class 按钮的点击事件
		$('.fa-bullhorn').off('click').on('click', function() {
			// 手动调用 Layer 弹窗，直接加载 URL（绕过 Modal 组件的内置逻辑）
			layer.open({
				type: 2, // type=2 表示加载 iframe URL
				title: '弹窗内容',
				shade: 0.3, // 遮罩透明度
				area: ['50%', '70%'], // 对应 lg 尺寸（可自定义）
				content: "/admin/SystemNotices", // 目标 URL
				maxmin: true, // 支持最大化（可选）
			});
		});
	});
JS);
});
$iframeHtml = <<<HTML
	<iframe 
	src="SystemNotices" 
	style="width: 100%; height: 600px; border: none; overflow: auto;"
	frameborder="0"
	></iframe>
HTML;
$modal = Modal::make()
->title('通知列表')
->lg()
->body($iframeHtml)
->button('<i class="fa fa-bullhorn"></i>');
$modalFullHtml = $modal->render();
// 将弹窗添加到导航栏右侧（规范渲染，自动绑定事件）
$navbar->right($modalFullHtml);
```
3. 数据仓库列表
```
public function get(Grid\Model $model)
{
    // 获取当前页数
    $currentPage = $model->getCurrentPage();
    // 获取每页显示行数
    $perPage = $model->getPerPage();
    // 3. 正确计算总条数和分页数据（核心修复）
    $total = ProductHotelOrderModel::query()->count();
    // 正确的分页逻辑：skip(跳过前N条) + take(取N条)
    $list = ProductHotelOrderModel::query()
        ->select('id','hotel_id','warehouse_id',DB::raw('SUBSTR(sign_time,1,10) as sign_time'),'status','created_at')
        ->groupBy(DB::raw('SUBSTR(sign_time,1,10)'),'hotel_id')->skip(($currentPage - 1) * $perPage)
        ->take($perPage)
        ->orderBy('created_at','desc')
        ->get()
        ->each(function ($item) {
            $driverUserId = WarehouseDriverPathModel::query()
                ->whereJsonContains("content", ["id" => $item['hotel_id']])
                ->value('user_id');
            $item['user_id'] = $driverUserId;
            return $item;
        });
    // dump($list->toArray());exit;
    // 4. 构造Dcat Admin需要的分页实例
    return $model->makePaginator($total, $list);
}
```
4. 接口频繁出现429 Too Many Requests
```app\Providers\RouteServiceProvider.php
public function boot(): void
{
	RateLimiter::for('api', function (Request $request) {
		return Limit::perMinute(600)->by($request->user()?->id ?: $request->ip());
	});
...
}
```
5. 全屏
```
public function index(Content $content)
	{
		return $content
				->header('系统通知')
				// 隐藏头部
				->header(false)
				// 设置面包屑
				->breadcrumb('通知')
				// 全屏(隐藏左侧菜单、头部、面包屑)
				->full()  
				->body($this->grid());
	}
```
6. COS上传带后缀，伪静态
```
    /**
        * 资讯文件上传（multipleFile）：在存储值中带上原始文件名
        * 返回 path = JSON{"url":,"name":原文件名}，供前端存储与接口回传真实文件名
        * @return \Illuminate\Http\JsonResponse
    */
    public function informationFileUpload()
    {
        $disk = $this->disk('local');
        if ($this->isDeleteRequest()) {
            return $this->deleteFileAndResponse($disk);
        }
        if ($file = $this->file()) {
            $secretId = "secretId";
            $secretKey = "secretKey";
            $region = "region";

            $cosClient = new \Qcloud\Cos\Client(
                array(
                    'region' => $region,
                    'schema' => 'https',
                    'credentials' => array(
                        'secretId' => $secretId,
                        'secretKey' => $secretKey
                    )
                )
            );
            try {
                $originalName = $file->getClientOriginalName();
                $result = $cosClient->putObject(array(
                    'Bucket' => 'Bucket',
                    'Key' => date('YmdHis') . rand(0000, 9999) . '.' . $file->getClientOriginalExtension(),
                    'Body' => fopen($file, 'rb'),
                ));
                $url = 'https://' . $result->toArray()['Location'];
                // Dcat 只把 data.id 作为存储值写入 files 列，故 id/path/url 都保持纯 URL（原生格式，后台编辑/预览才正常）
                // 原文件名通过缓存按 URL 暂存，待表单保存时反查写入 file_names 列
                $extension = $file->getClientOriginalExtension();
                $data = [
                    'id' => $url,
                    'name' => $originalName, // 后台列表显示原文件名
                    'path' => $url,
                    'url' => $url, // 预览
                    'type' => $file->getClientMimeType(),
                    'size' => Number::fileSize($file->getSize()),
                    'suffix' => $extension,
                ];
                Cache::store('file')->put('file_meta_' . md5($url), $data, now()->addHour());
                return success(['status' => true, 'msg' => '上传成功', 'data' => $data]);
            } catch (\Exception $e) {
                return success(['status' => false, 'msg' => $e->getMessage(), 'data' => []]);
            }
        } else {
            return success(['code' => true, 'message' => '未找到文件', 'data' => []]);
        }
    }

    // 控制器使用
    $form->multipleFile('files')
        ->accept('jpg,png,gif,jpeg,pdf,xlsx,xls,docx,doc,pptx,ppt,txt')
        ->autoUpload()
        ->url('informationFileUpload')
        ->saving(function ($files) {
            return implode(',', $files);
        });
    $form->hidden('filesdata');
    $form->saving(function (Form $form) {
        // 获取提交上来的 files 文件数组
        $files = $form->input('files');

        // 格式兼容：如果传上来的是 JSON 字符串或逗号分割字符串，自动转为数组
        if (is_string($files)) {
            $files = json_decode($files, true) ?? array_filter(explode(',', $files));
        }

        $detailedFiles = [];

        if (is_array($files)) {
            foreach ($files as $url) {
                if (!$url){
                    continue;
                }

                $meta = \Cache::get('file_meta_' . md5($url));
                if (!empty($meta)) {
                    $detailedFiles[] = $meta;
                } else {
                    $detailedFiles[] = [
                        'url' => $url,
                        'name' => pathinfo($url, PATHINFO_BASENAME),
                        'suffix' => pathinfo($url, PATHINFO_EXTENSION),
                    ];
                }
            }
        }

        // 调试打印
        // print_r($detailedFiles); exit;

        // 赋值隐藏域字段
        $form->input('filesdata', json_encode($detailedFiles, JSON_UNESCAPED_UNICODE));
    });
```