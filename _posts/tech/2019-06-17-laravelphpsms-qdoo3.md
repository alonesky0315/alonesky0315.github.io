---
layout: post
title: Laravel框架利用phpsms对接腾讯云短信
category: PHP
keywords: 
tags: PHP 插件
description: 
---

config/phpsms.php
```
'Qcloud' => [
	'send_url'=>'',
	'appid' => 'appid',
	'appkey' => 'appkey',
	'smsSign' => '签名',
],
```
vendor\toplan\phpsms\src\phpsms\agents\QcloudAgent.php
```
<?php
namespace Toplan\PhpSms;
/**
 * Class QcloudAgent
 *
 * @property string $sendUrl
 * @property string $appKey
 * @property string $secretKey
 * @property string $smsFreeSignName
 * @property string $calledShowNum
 */
class QcloudAgent extends Agent{
    private $time;
    private $random;
    public function sendSms($to, $content, $tempId, array $data){
        $this->sendTemplateSms($to, $tempId, $data);
    }
    public function sendTemplateSms($to, $tempId, array $data){
        $this->time=time();
        $this->random=$this->getRandom();
        $tel = new \stdClass();
        $tel->nationcode = ""."86";
        $tel->mobile = "".$to;
        $params = [
            'params'    => (array)$data['code'],
            'sign'      => $this->smsSign,
            'tel'       => $tel,
            'tpl_id'    => $tempId,
            'time'      => $this->time,
            'random'    => $this->random,
        ];
        $this->request($params);
    }
    public function voiceVerify($to, $code, $tempId, array $data){
        
        $this->request($params);
    }
    protected function request(array $params){
        $sendUrl = $this->send_url?$this->send_url: 'https://yun.tim.qq.com/v5/tlssmssvr/sendsms?sdkappid='. $this->appid.'&random='.$this->random;
        $params=$this->createParams($params);
        $result = $this->sendCurlPost($sendUrl,(object)$params);
        $this->setResult($result, $this->genResponseName($params['method']));
    }
    protected function setResult($result, $callbackName){
        if ($result['request']) {
            $result = json_decode($result['response'], true);
            if (isset($result[$callbackName]['result'])) {
                $result = $result[$callbackName]['result'];
                print_r($result);exit;
                $this->result(Agent::SUCCESS, (bool) $result['success']);
                $this->result(Agent::INFO, json_encode($result));
                $this->result(Agent::CODE, $result['err_code']);
            } elseif (isset($result['error_response'])) {
                $error = $result['error_response'];
                $this->result(Agent::INFO, json_encode($error));
                $this->result(Agent::CODE, $error['code']);
            }
        } else {
            $this->result(Agent::INFO, '请求失败');
        }
    }
    /**
     * 生成随机数
     *
     * @return int 随机数结果
     */
    protected function getRandom(){
        return rand(100000, 999999);
    }
    protected function createParams(array $params){
        $mobile=$params['tel']->mobile;
        $params = array_merge([
            'sig'  =>hash("sha256","appkey=".$this->appkey."&random=".$this->random."&time=".$this->time."&mobile=".$mobile)
        ],$params);
        return $params;
    }
    /**
     * 发送请求
     *
     * @param string $url      请求地址
     * @param array  $dataObj  请求内容
     * @return string 应答json字符串
     */
    protected function sendCurlPost($url,$dataObj){
        $curl = curl_init();
        curl_setopt($curl, CURLOPT_URL, $url);
        curl_setopt($curl, CURLOPT_HEADER, 0);
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($curl, CURLOPT_POST, 1);
        curl_setopt($curl, CURLOPT_CONNECTTIMEOUT, 60);
        curl_setopt($curl, CURLOPT_POSTFIELDS, json_encode($dataObj));
        curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, 0);
        curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, 0);
        $ret = curl_exec($curl);
        if (false == $ret) {
            // curl_exec failed
            $result = "{ \"result\":" . -2 . ",\"errmsg\":\"" . curl_error($curl) . "\"}";
        } else {
            $rsp = curl_getinfo($curl, CURLINFO_HTTP_CODE);
            if (200 != $rsp) {
                $result = "{ \"result\":" . -1 . ",\"errmsg\":\"". $rsp
                        . " " . curl_error($curl) ."\"}";
            } else {
                $result = $ret;
            }
        }
        curl_close($curl);
        return $result;
    }
    public function sendContentSms($to, $content){
    }
}
```