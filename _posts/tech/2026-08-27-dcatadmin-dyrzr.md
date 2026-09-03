---
layout: post
title: DcatAdmin 实时消息弹窗
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

#### DcatAdmin 实时消息弹窗

1. 添加弹窗(app\Admin\bootstrap.php)
```
// 1. 判断：必须是【已登录】 + 【不是登录页】 + 【必须是超级管理员】
if (Admin::user() && Admin::user()->isAdministrator() && !request()->is('admin/auth/login*')) {
    Admin::script(<<<JS
        if (window.self !== window.top) return;

        if (!window.hasNoticeIntervalStarted) {
            window.hasNoticeIntervalStarted = true;

            window.noticeIntervalTimer = setInterval(function () {
                // 页面切到登录页时停止轮询
                if (window.location.pathname.indexOf('/admin/auth/login') !== -1) {
                    clearInterval(window.noticeIntervalTimer);
                    return;
                }

                $.ajax({
                    url: '/admin/checkSystemNotice',
                    method: 'GET',
                    dataType: 'json',
                    success: function (res) {
                        if (!res) return;

                        // 1. 定义通知类型与消除/处理接口的映射
                        var noticeTypes = [
                            { 
                                key: 'orderNoticeCount', 
                                text: '条订单通知！', 
                                type: '1' 
                            },
                            { 
                                key: 'productAuditNoticeCount', 
                                text: '条商品审核通知！', 
                                type: '2' 
                            },
                            { 
                                key: 'reserveMushroomNoticeCount', 
                                text: '条菌棒预约通知！', 
                                type: '3' 
                            }
                        ];

                        var hasAnyNotice = false;

                        // 清理上一轮未处理的旧弹窗
                        toastr.clear();

                        // 2. 循环判断并弹出提示
                        noticeTypes.forEach(function (item) {
                            var count = res[item.key];
                            if (count && count > 0) {
                                hasAnyNotice = true;
                                
                                Dcat.warning('收到 ' + count + ' ' + item.text, null, {
                                    timeOut: 0,          // 永不自动消失
                                    extendedTimeOut: 0,  // 移开鼠标不消失
                                    closeButton: true,   // 显示关闭按钮
                                    tapToDismiss: false, // 禁用点击框体消失
                                    
                                    onclick: function (e) {
                                        $.ajax({
                                            url: '/admin/dismissNotice',
                                            method: 'POST',
                                            data: {
                                                type: item.type,
                                                _token: Dcat.token
                                            },
                                            success: function (response) {
                                                console.log(item.type + ' 通知已处理/忽略');
                                            }
                                        });
                                        var link='/admin/ProductHotelOrderFull';
                                        if(item.type == 2){
                                            link='/admin/FactoryProductFull';
                                        }else if(item.type == 3){
                                            link='/admin/MushroomReserveFull';
                                        }
                                        layer.open({
                                            type: 2,
                                            area: ['100%', '100%'],
                                            content: link
                                        });
                                    }
                                });
                            }
                        });

                        // 3. 有任意新通知时统一播放提示音
                        if (hasAnyNotice) {
                            new Audio('/notice.mp3').play().catch(function (e) {});
                        }
                    },
                    // 4. 401/403 自动停掉轮询（如 Session 过期或权限被关）
                    error: function (xhr) {
                        if (xhr.status === 401 || xhr.status === 403) {
                            console.warn('登录已失效或无权限，停止轮询通知。');
                            clearInterval(window.noticeIntervalTimer);
                        }
                    }
                });
            }, 10000);
        }
JS
    );
}
```
2. 路由(app\Admin\routes.php)
```
$router->get('MushroomReserveFull', 'MushroomReserveController@full');
```
3. 控制器
```
use Dcat\Admin\Layout\Content;
public function full(Content $content)
{
		return $content
			->header('排产预约管理')
			->full()// 全屏，隐藏左侧菜单
			->body($this->grid());
}
public function checkSystemNotice()
{
    // 优化1：直接使用 count() 聚合查询，性能比 get()->toArray() 高几十倍，减少内存占用
    $orderNoticeCount = SystemNoticeModel::query()
        ->where(['to' => 5, 'is_read' => 0, 'type' => 1])
        ->count();
    $productAuditNoticeCount = SystemNoticeModel::query()
        ->where(['to' => 5, 'is_read' => 0, 'type' => 2])
        ->count();
    $reserveMushroomNoticeCount = SystemNoticeModel::query()
        ->where(['to' => 5, 'is_read' => 0, 'type' => 3])
        ->count();
    if ($orderNoticeCount === 0 && $productAuditNoticeCount === 0 && $reserveMushroomNoticeCount === 0) {
        return response()->json([
            'orderNoticeCount' => 0,
            'productAuditNoticeCount' => 0,
            'reserveMushroomNoticeCount' => 0,
        ]);
    }
    return response()->json([
        'orderNoticeCount' => $orderNoticeCount,
        'productAuditNoticeCount' => $productAuditNoticeCount,
        'reserveMushroomNoticeCount' => $reserveMushroomNoticeCount,
    ]);
}
public function dismissNotice()
{
    // 兼容：既支持传递按类型批量标记已读 (type)，也支持按单条 ID 标记已读 (id)
    $type = request()->input('type');
    if ($type) {
        // 当点击前端某类 Toastr 弹窗的关闭按钮时，将该类型所有发送给 5 的未读通知全部标为已读
        SystemNoticeModel::query()
            ->where(['to' => 5, 'is_read' => 0, 'type' => $type])
            ->update(['is_read' => 1]);
    }
    return response()->json(['success' => true]);
}
```