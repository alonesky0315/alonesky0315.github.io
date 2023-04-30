---
layout: post
title: PHP AES加密
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

PHP AES加密

```
<?php

namespace app\common\model;

class Aes
{
    private $hex_iv;
    private $key = '';// AES加密密钥

    function __construct()
    {
        $cipher_methods=openssl_get_cipher_methods();// PHP支持的加密方式
        // write_log($cipher_methods);
        $hex_iv_length = openssl_cipher_iv_length('AES-256-ECB');// 加密长度
        $this->hex_iv = openssl_random_pseudo_bytes($hex_iv_length);// 随机字符
    }
	 // 加密字符
    public function encrypt($input = '')
    {
        return base64_encode(openssl_encrypt($input, 'AES-256-ECB', $this->key, OPENSSL_RAW_DATA,$this->hex_iv));
    }
		// 解密字符
    public function decrypt($input = '')
    {
        return openssl_decrypt(base64_decode($input), 'AES-256-ECB', $this->key, OPENSSL_RAW_DATA, $this->hex_iv);
    }
}
```