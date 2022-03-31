---
layout: post
title: Laravel框架利用phpsms对接阿里大鱼短信
category: PHP
keywords: 
tags: PHP 插件
description: 
---

### config/phpsms.php   
```
'Alidayu' => [
	'accessKeyId' => 'accessKeyId',
	'accessKeySecret' => 'accessKeySecret',
	'SignName' => '签名',
],
```
### vendor\composer\autoload_classmap.php
```
'Alidayu' => $vendorDir . '/toplan/phpsms/src/phpsms/lib/Alidayu.php',
```
### vendor\composer\autoload_static.php
* public static $classMap = array ()

```
'Alidayu' => __DIR__ . '/..' . '/toplan/phpsms/src/phpsms/lib/Alidayu.php',
```
### vendor\toplan\phpsms\src\phpsms\lib\Alidayu.php
##### (Alidayu精简版 SignatureHelper.php文件)
```
<?php
/**
 * 签名助手 2017/11/19
 *
 * Class SignatureHelper
 */
class Alidayu {
    /**
     * 生成签名并发起请求
     *
     * @param $accessKeyId string AccessKeyId (https://ak-console.aliyun.com/)
     * @param $accessKeySecret string AccessKeySecret
     * @param $domain string API接口所在域名
     * @param $params array API具体参数
     * @param $security boolean 使用https
     * @param $method boolean 使用GET或POST方法请求，VPC仅支持POST
     * @return bool|\stdClass 返回API接口调用结果，当发生错误时返回false
     */
    public function request($accessKeyId, $accessKeySecret, $domain, $params, $security=false, $method='POST') {
        $apiParams = array_merge(array (
            "SignatureMethod" => "HMAC-SHA1",
            "SignatureNonce" => uniqid(mt_rand(0,0xffff), true),
            "SignatureVersion" => "1.0",
            "AccessKeyId" => $accessKeyId,
            "Timestamp" => gmdate("Y-m-d\TH:i:s\Z"),
            "Format" => "JSON",
        ), $params);
        ksort($apiParams);
        $sortedQueryStringTmp = "";
        foreach ($apiParams as $key => $value) {
            $sortedQueryStringTmp.= "&".$this->encode($key)."=".$this->encode($value);
        }
        $stringToSign = "${method}&%2F&" . $this->encode(substr($sortedQueryStringTmp, 1));
        $sign = base64_encode(hash_hmac("sha1", $stringToSign, $accessKeySecret . "&",true));
        $signature = $this->encode($sign);
        $url = ($security ? 'https' : 'http')."://{$domain}/";
        try {
            $content = $this->fetchContent($url, $method, "Signature={$signature}{$sortedQueryStringTmp}");
            return json_decode($content);
        } catch( \Exception $e) {
            return false;
        }
    }
    private function encode($str){
        $res = urlencode($str);
        $res = preg_replace("/\+/", "%20", $res);
        $res = preg_replace("/\*/", "%2A", $res);
        $res = preg_replace("/%7E/", "~", $res);
        return $res;
    }
    private function fetchContent($url, $method, $body) {
        $ch = curl_init();
        if($method == 'POST') {
            curl_setopt($ch, CURLOPT_POST, 1);//post提交方式
            curl_setopt($ch, CURLOPT_POSTFIELDS, $body);
        } else {
            $url .= '?'.$body;
        }
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_TIMEOUT, 5);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($ch, CURLOPT_HTTPHEADER, array(
            "x-sdk-client" => "php/2.0.0"
        ));
        if(substr($url, 0,5) == 'https') {
            curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
            curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
        }
        $rtn = curl_exec($ch);
        if($rtn === false) {
            // 大多由设置等原因引起，一般无法保障后续逻辑正常执行，
            // 所以这里触发的是E_USER_ERROR，会终止脚本执行，无法被try...catch捕获，需要用户排查环境、网络等故障
            trigger_error("[CURL_" . curl_errno($ch) . "]: " . curl_error($ch), E_USER_ERROR);
        }
        curl_close($ch);
        return $rtn;
    }
}
```
### vendor\toplan\phpsms\src\phpsms\agents\AlidayuAgent.php
```
<?php
namespace Toplan\PhpSms;
/**
 * Class AlidayuAgent
 *
 * @property string $sendUrl
 * @property string $appKey
 * @property string $secretKey
 * @property string $smsFreeSignName
 * @property string $calledShowNum
 */
class AlidayuAgent extends Agent{
    public function sendSms($to, $content, $tempId, array $data){
        $this->sendTemplateSms($to, $tempId, $data);
    }
    public function sendTemplateSms($to, $tempId, array $data){
        $params = [
            'accessKeyId'       => $this->accessKeyId,
            'accessKeySecret'   => $this->accessKeySecret,
            'SignName'          => $this->SignName,
            'TemplateCode'      => $tempId,
            'TemplateParam'     => json_encode($data),
            'PhoneNumbers'      => $to
        ];
        $this->request($params);
    }
    public function voiceVerify($to, $code, $tempId, array $data){
        
        $this->request($params);
    }
    protected function request(array $params){
        $params = $this->createParams($params);
        //print_r($params);exit;
        $helper = new \Alidayu();
        $result = $helper->request(
            $this->accessKeyId,
            $this->accessKeySecret,
            "dysmsapi.aliyuncs.com",
            array_merge($params, array(
                "RegionId" => "cn-hangzhou",
                "Action" => "SendSms",
                "Version" => "2017-05-25",
            )
        ),$params['security']);
        $this->setResult($result);
    }
    protected function createParams(array $params){
        $params = array_merge([
            'security'          => 'false',
            'OutId'             => time(),
            'SmsUpExtendCode'   => date('His'),
        ], $params);
        return $params;
    }
    protected function setResult($result){
        if ($result->Code=='OK') {
            $this->result(Agent::SUCCESS,true);
        } else {
            $this->result(Agent::SUCCESS,false);
        }
        $this->result(Agent::INFO,$result->Message);
        $this->result(Agent::CODE,$result->Code);
    }
    public function sendContentSms($to, $content){
    }
}
```