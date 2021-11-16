---
layout: post
title: Fastadmin上传图片到COS
category: 技术
keywords: Fastadmin,基础教程,上传图片
tags: Fastadmin,基础教程,上传图片
description: Fastadmin上传图片到COS
---

#### 安装腾讯云COS扩展
```
composer require qcloud/cos-sdk-v5 ~2.* 
```
#### 修改api/controller/Common.php upload方法(约191行上传本地成功之后)
```
//上传到oss
$config = [
    'region'=>'ap-shanghai',
    'bucket'=>'bucket',
    'secretid'=>'secretid',
    'secretkey'=>'secretkey',
];
$cosConfig = array(
    'region'      => $config['region'],
    'schema'      => 'https', //协议头部，默认为http
    'credentials' => array(
        'secretId'  => $config['secretid'],
        'secretKey' => $config['secretkey']
    )
);
$cosClient = new \Qcloud\Cos\Client($cosConfig);
try {
    if(empty($fileExists)){
        $result = $cosClient->putObject([
            'Bucket' => $config['bucket'], //格式：BucketName-APPID
            'Key' => $uploadDir . $splInfo->getSaveName(),
            'Body' => fopen(ROOT_PATH . 'public' . $uploadDir.$fileName, "rb")
        ]);
    }
} catch (\Exception $e) {
    // 请求失败
    echo($e);
}
// dump($result);exit;
```