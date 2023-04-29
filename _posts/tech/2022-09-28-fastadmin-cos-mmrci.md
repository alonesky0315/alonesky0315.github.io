---
layout: post
title: fastadmin 使用腾讯云COS插件添加自定义参数
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

一、 插件配置

在使用腾讯COS之前请先到腾讯云存储对象添加一个存储桶，然后到存储桶详情，获取到以下相关配置信息填写到后台插件管理配置中去。

| 后台配置	| 腾讯云配置 |
| :----: | :---- |
| AppID、SecretId、SecretKey | 个人信息->访问管理->API密钥管理 |
| 存储桶名称 | 概览->基本信息->存储桶名称 |
| 地域名称 | 概览->基本信息->所属地域(英文) |
| 上传接口地址 | 概览->域名信息->访问域名 |
| CDN地址 | 请使用腾讯云存储详情->域名与传输管理->默认CDN加速域名中的加速域名或自定义CDN加速域名中的域名 |

在腾讯云COS存储桶的安全管理的跨域访问CORS设置，添加一个全局跨域规则便于开发，上线后可修改为只允许你指定的域名进行跨域上传，跨域配置如图：
![image.png](https://blog.alonesky.com/storage/article/2022/09/28/JOL1cjMVAVsgAiMP5te3F3JflLV4IKl0FwZAF1y7.png)

跨域配置规则请参考腾讯云官方跨域配置：https://cloud.tencent.com/document/product/436/13318

在腾讯COS存储桶域名与传输管理的自定义加速域名中添加一个你自己的CDN域名

在腾讯云COS存储桶的基础配置的静态网站中开启访问

在腾讯云COS存储桶的权限管理的存储桶访问权限中开启公有读私有写

在腾讯云CDN内容分发网络的域名管理找到我们自定义的域名管理，进入到高级配置中的HTTP Header配置，添加一项参数为Content-Disposition，取值为inline;filename=FileName.txt的项

配置成功后即可在后台直接上传文件至腾讯COS。

文档最后更新时间：2022-08-23 15:51:46

二、 自定义参数

```html
<div class="form-group">
    <label class="control-label col-xs-12 col-sm-2">{:__('Image')}:</label>
    <div class="col-xs-12 col-sm-8">
        <div class="input-group">
            <input id="c-image" class="form-control" size="50" name="row[image]" type="text">
            <div class="input-group-addon no-border no-padding">
                <span><button type="button" id="plupload-image" data-utype="ads" class="btn btn-danger plupload" data-input-id="c-image" data-mimetype="image/gif,image/jpeg,image/png,image/jpg,image/bmp" data-multiple="false" data-preview-id="p-image"><i class="fa fa-upload"></i> {:__('Upload')}</button></span>
                <span><button type="button" id="fachoose-image" data-utype="ads"class="btn btn-primary fachoose" data-input-id="c-image" data-mimetype="image/*" data-multiple="false"><i class="fa fa-list"></i> {:__('Choose')}</button></span>
            </div>
            <span class="msg-box n-right" for="c-image"></span>
        </div>
        <ul class="row list-inline plupload-preview" id="p-image"></ul>
    </div>
</div>
```
修改文件public/assets/js/addons.js 大约在192行
```js
Fast.api.ajax({
    url: "/addons/cos/index/params",
    data: {
        method: 'POST',
        category: category,
        md5: md5,
        name: file.name,
        type: file.type,
        size: file.size,
        chunk: chunk,
        utype: $(that.element).data("utype") || '',
        chunksize: that.options.chunkSize,
        costoken: Config.upload.multipart.costoken
    },
}, function (data) {
   ...
}, function () {
    that.removeFile(file);
});
```
修改文件addons/cos/controller/Index.php添加接收值更改返回值
```php
public function params(){
    ...
    $utype = $this->request->post('utype');
    ...
    $params['utype'] = $utype;
    $this->success('', null, $params);
    return;
}

/**
 * 服务器中转上传文件
 * 上传分片
 * 合并分片
 * @param bool $isApi
 */
public function upload($isApi = false){
    ...
    $utype = $this->request->post("utype");
    if ($chunkid) {
        ...
    }else{
        ...
        if (strpos($url, '.jpg') || strpos($url, '.png')|| strpos($url, '.bmp')|| strpos($url, '.jpeg')|| strpos($url, '.gif')) {
            if(in_array($utype,['ads','logo','navigation'])){
                $this->success("上传成功", '', ['url' => $url, 'fullurl' => cdnurl($url, true)]);
            }else{
                $this->success("上传成功", '', ['url' => $url.'/watermark', 'fullurl' => cdnurl($url.'/watermark', true)]);
            }
        }else{
            $this->success("上传成功", '', ['url' => $url, 'fullurl' => cdnurl($url, true)]);
        }
        return;
    }
}
```

> 备注：
> 
> 腾讯云COS和百度UMeditor编辑器本地上传图片后图片路径出现问题   
> 修改文件public/assets/addons/umeditor/dialogs/image/image.js 约105行
```
var $img = $("<img src='" + editor.options.imagePath + 
url.replace(editor.options.imagePath,'') + "' class='edui-image-pic' />"),
$item = $("<div class='edui-image-item edui-image-upload-item'><div class='edui-image-close'></div></div>").append($img);
```
> 微信小程序使用腾讯云cos图片
> 小程序的网络请求的 referer 是固定格式为：https://servicewechat.com/{appid}/{version}/page-frame.html。
> 如果存储桶打开了防盗链限制，并且需要允许小程序加载 COS 图片，请在 对象存储控制台 配置防盗链白名单：servicewechat.com。

参考链接：

腾讯云COS设置防盗链：
[https://cloud.tencent.com/document/product/436/13319](https://cloud.tencent.com/document/product/436/13319)

原文链接：[https://doc.fastadmin.net/cos](https://doc.fastadmin.net/cos)