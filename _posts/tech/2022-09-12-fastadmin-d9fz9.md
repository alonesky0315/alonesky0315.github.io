---
layout: post
title: FastAdmin生成压缩文件
category: PHP
keywords: 
tags: 常识 PHP HTML JavaScript
description: 
---

FastAdmin生成压缩文件

```php
public function download($ids = null){
    if (!$this->request->isPost()) {
        if (!class_exists('ZipArchive')) {
            $this->error("服务器缺少php-zip组件，无法进行下载操作");
        }
        $this->error(__("Invalid parameters"));
    }
    $fileDir = ROOT_PATH . 'public' . DS . 'taskSign'. DS;
    $ids = $ids ? $ids : $this->request->post("ids");
    $taskLogList=model('\app\admin\model\TaskLog')->where('task_id','in',$ids)->field('task_id,sign_time,sign_thumb')->select();
    $zip = new ZipArchive();
    if (!is_dir($fileDir)) {
        @mkdir($fileDir, 0755);
    }
    $name = "taskSign-".time();
    $filename = $fileDir . $name . ".zip";
    if ($zip->open($filename, ZIPARCHIVE::CREATE) !== true) {
        throw new Exception("Could not open <$filename>\n");
    }
    foreach ($taskLogList as $key=> $item) {
        $fileInfo=pathinfo($item['sign_thumb']);
        $zip->addFile(ROOT_PATH . 'public' . DS .str_replace(Request()->domain(),'',$item['sign_thumb']),
            date('ymd',$item['sign_time']).DS.$item['task_id'].'-'.$fileInfo['basename']);
    }
    $zip->close();
    $this->success('下载成功',Request()->domain().DS.'taskSign'.DS.$name . ".zip");
}

#html
<a href="javascript:;" class="btn btn-warning btn-download {:$auth->check('task/download')?'':'hide'}" title="{:__('Download')}" ><i class="fa fa-download"></i> {:__('Download')}</a>

#js
$(document).on('click', '.btn-download', function () {
    let ids=Table.api.selectedids(table);
    if (ids.length==0){
        Toastr.error('请先选择任务');
        return false;
    }
    $.ajax({
        type: 'POST',
        url: 'task/download',
        data: {ids:ids},
        dataType: 'json',
        success: function (res) {
            if(res.code == 1) {
                window.location.href=res.url;
                // Fast.api.open(res.url,'下载打卡记录图片',{
                //     success: function (layero, index) {
                //
                //         Layer.close(index);
                //         console.log(7777);
                //         layer.close(index);
                //     }
                // });
            } else {
                Toastr.error(res.msg);
            }
        },
        fail:function (){
            Toastr.error('查询结束时间失败');
        }
    });
});

```

参考链接：

[https://blog.csdn.net/weixin_35652574/article/details/115568438](https://blog.csdn.net/weixin_35652574/article/details/115568438)

[https://blog.csdn.net/qq15577969/article/details/115733504](https://blog.csdn.net/qq15577969/article/details/115733504)