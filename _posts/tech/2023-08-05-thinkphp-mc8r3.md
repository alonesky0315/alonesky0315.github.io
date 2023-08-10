---
layout: post
title: ThinkPHP框架判断是否关注了微信公众号
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

> 通过接口可以获得subscribe字段判断是否关注，subscribe 0=未关注 1=已关注
> 
> 通过授权code获得openid（服务号订阅号都可，网上都说订阅号获取不到openid，我使用订阅号获取到了.╯︿╰.）

PHP 代码

```php
use app\common\model\Article as ArticleModel;
use GuzzleHttp\Client;
use think\Cache;

protected $appid = 'appid';
protected $secret = 'secret';

// 文章详情
// 我的授权放在这里面的
public function info()
{
    $ArticleModel = new ArticleModel();
    $article_id = $this->request->param('id/d');
    $code = $this->request->param('code');
    if (empty($article_id)) {
        $this->error('请重新扫描二维码');
    }
    // 文章链接
    $authorizeUrl = request()->domain() . '/index/article/info/id/' . $article_id;
    if (!isset($code)) {
        $redirect_uri = urlencode($authorizeUrl);
        $url = 'https://open.weixin.qq.com/connect/oauth2/authorize?appid=' . $this->appid . '&redirect_uri=' . $redirect_uri . '&response_type=code&scope=snsapi_base&state=1#wechat_redirect';
        header("location:$url");
        exit();
    }
    // 文章详情
    $articleInfo = $ArticleModel
        ->field('id,title,introduction,content')
        ->where(['id' => $article_id, 'status' => 'normal'])
        ->find();
    if (empty($articleInfo)) {
        $this->error('文章不存在');
    }
    // 获取openid
    $getOpenIdResponse = self::getJson('https://api.weixin.qq.com/sns/oauth2/access_token?appid=' . $this->appid . '&secret=' . $this->secret . '&code=' . $code . '&grant_type=authorization_code');
    $isFollow = false;
    // print_r($getOpenIdResponse);
    if (!empty($getOpenIdResponse->openid)) {
        $isFollow = self::getFollowStatus($getOpenIdResponse->openid);
    } else {
        header("location:$authorizeUrl");
    }
    // 跳转链接(链接跳过去没有关注按钮了，直接打开可以)
    // if(empty($isFollow)){
    //     header("location:https://mp.weixin.qq.com/mp/profile_ext?action=home&__biz=MzU0MTQwODM2NQ==");
    // }
    // dump($isFollow);exit;
    $ArticleModel->where(['id' => $article_id])->setInc('reading');
    $this->view->assign("articleInfo", $articleInfo);
    $this->view->assign("isFollow", $isFollow);
    $this->view->assign("code", $code);
    return $this->view->fetch();
}

// 获取是否关注
public function getFollowStatus(string $openId = '')
{
    if (!Cache::get('access_token')) {
        $getAccessTokenResponse = self::getJson('https://api.weixin.qq.com/cgi-bin/token?appid=' . $this->appid . '&secret=' . $this->secret . '&grant_type=client_credential');
        Cache::set('access_token', $getAccessTokenResponse->access_token, 6000);
    }
    $access_token = Cache::get('access_token');
    // write_log($access_token);
    $response = self::getJson('https://api.weixin.qq.com/cgi-bin/user/info?access_token=' . $access_token . '&openid=' . $openId . '');
    // dump($response);
    $isFollow = !empty($response->subscribe) ? true : false;
    return $isFollow;
}

// 请求接口
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
```

参考链接：[https://www.cnblogs.com/mracale/p/9318349.html](https://www.cnblogs.com/mracale/p/9318349.html)