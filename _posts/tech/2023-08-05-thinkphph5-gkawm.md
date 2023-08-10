---
layout: post
title: ThinkPHP之H5网页在微信中分享到好友及朋友圈(新)
category: PHP
keywords: HTML PHP Common
tags: HTML PHP Common
description: 
---

> ThinkPHP之H5网页在微信中分享到好友及朋友圈

PHP页面
```php
use GuzzleHttp\Client;
use think\Cache;

protected $appid = 'appid';
protected $secret = 'secret';

// 获取微信参数
public function getSignPackage(int $article_id = 0)
{
    $jsapiTicket = $this->getJsApiTicket();
    // 注意 URL 一定要动态获取，不能 hardcode.
    $url = request()->domain() . '/index/article/info/id/' . $article_id;
    $timestamp = time();
    $nonceStr = $this->createNonceStr();
    // 这里参数的顺序要按照 key 值 ASCII 码升序排序
    $string = 'jsapi_ticket=' . $jsapiTicket . '&noncestr=' . $nonceStr . '&timestamp=' . $timestamp . '&url=' . $url . '';
    $signature = sha1($string);
    $data = [
        "appId" => $this->appid,
        "nonceStr" => $nonceStr,
        "timestamp" => $timestamp,
        "url" => $url,
        "signature" => $signature,
        "rawString" => $string,
        "link" => $url,
        "imgUrl" => request()->domain() . '/assets/img/share_img.jpg'
    ];
    $this->success('获取成功', null, $data);
}
public function getJson($url = '')
{
    $client = new Client();
    $response = $client->request('GET', $url);
    if ($response->getStatusCode() != 200) {
        $this->error('请求失败：' . $response->getBody()->getContents());
    } else {
        return json_decode($response->getBody()->getContents());
    }
}
private function getJsApiTicket()
{
    // jsapi_ticket 应该全局存储与更新，以下代码以写入到文件中做示例
    $data = Cache::get('jsapi_ticket');
    if (!Cache::get('jsapi_ticket')) {
        if (Cache::get('access_token')) {
            $access_token = Cache::get('access_token');
        } else {
            $getAccessTokenResponse = self::getJson('https://api.weixin.qq.com/cgi-bin/token?appid=' . $this->appid . '&secret=' . $this->secret . '&grant_type=client_credential');
            Cache::set('access_token', $getAccessTokenResponse->access_token, 6000);
            $access_token = Cache::get('access_token');
        }
        $getJsApiTicketResponse = self::getJson('https://api.weixin.qq.com/cgi-bin/ticket/getticket?type=jsapi&access_token=' . $access_token . '');
        // print_r($getJsApiTicketResponse);exit;
        Cache::set('jsapi_ticket', $getJsApiTicketResponse->ticket, 6000);
    }
    $jsapi_ticket = Cache::get('jsapi_ticket');
    return $jsapi_ticket;
}
private function createNonceStr($length = 16)
{
    $chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    $str = "";
    for ($i = 0; $i < $length; $i++) {
        $str .= substr($chars, mt_rand(0, strlen($chars) - 1), 1);
    }
    return $str;
}
```

HTML页面
```html
<script src="https://res.wx.qq.com/open/js/jweixin-1.6.0.js"></script>

<script>
$.ajax({
    type: "POST",
    url: "/Common/getSignPackage",
    data: { article_id: $('.article_id').val()},
    dataType: "json",
    success: function (result) {
        if (result.code == 1) {
            wx.config({
                // 开启调试模式,调用的所有api的返回值会在客户端alert出来，
                // 若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
                debug: false, 
                appId: result.data.appId, // 必填，公众号的唯一标识
                timestamp: result.data.timestamp, // 必填，生成签名的时间戳
                nonceStr: result.data.nonceStr, // 必填，生成签名的随机串
                signature: result.data.signature,// 必填，签名
                // 必填，需要使用的JS接口列表
               jsApiList: [
                    'showMenuItems', 
                    'updateAppMessageShareData', 
                    'updateTimelineShareData'
                ]
            });
            wx.ready(function () {   //需在用户可能点击分享按钮前就先调用
                wx.showMenuItems({
                    menuList: [
                        'menuItem:addContact',
                        'menuItem:refresh',
                        'menuItem:profile',
                    ] // 要显示的菜单项，所有menu项见附录3
                });
                wx.updateAppMessageShareData({
                    title: $('.article_title').text(), // 分享标题
                    desc: '', // 分享描述
                    // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
                    link: result.data.link, 
                    imgUrl: result.data.imgUrl, // 分享图标
                    success: function () {
                        // 设置成功
                    }
                });
                wx.updateTimelineShareData({
                    title: $('.article_title').text(), // 分享标题
                    desc: '', // 分享描述
                    // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
                    link: result.data.link,
                    imgUrl: result.data.imgUrl, // 分享图标
                    success: function () {
                        // 设置成功
                    }
                });
            });
        } else {
            layer.msg(result.msg);
        }
        // console.log(result);
    }
});
</script>
```