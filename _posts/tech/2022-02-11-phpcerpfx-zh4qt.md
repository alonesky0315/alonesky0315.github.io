---
layout: post
title: PHP读取.cer文件解析公钥证书.pfx证书
category: PHP
keywords: 
tags: 
description: 
---

php读取.cer文件

```
$filePath='./file/alipay.cer';
$certifFileContent = file_get_contents($filePath);
$certifPemContent = '-----BEGIN CERTIFICATE-----' . PHP_EOL. chunk_split(base64_encode($certifFileContent), 64, PHP_EOL). '-----END CERTIFICATE-----' . PHP_EOL;
```

php读取.fpx文件

```
$filePath='./file/alipay.fpx';
$pkcs12 = file_get_contents($filePath);
$response = openssl_pkcs12_read($pkcs12, $certs, '密码');
```