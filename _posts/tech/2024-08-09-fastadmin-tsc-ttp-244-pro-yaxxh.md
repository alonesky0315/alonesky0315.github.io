---
layout: post
title: FastAdmin 对接 TSC TTP-244 Pro打印机
category: HTML
keywords: Common JavaScript HTML
tags: Common JavaScript HTML
description: 
---

#### FastAdmin 对接 TSC TTP-244 Pro打印机
> 用的90mm*60mm的塑封标签纸，刚开始一直打印的两页，文字正好在标签中间
> 配置网页的打印机属性只有首次可以，后期又变成初始值，要么打印在顶部要么打印在底部

### 打印错乱和浏览器也有关系

1. 连接打印机并安装驱动
2. 设置打印首选项   
位置：设置-蓝牙和其他设备-打印机和扫描仪-打印机名称-打印首选项
![image.png](https://blog.alonesky.com/storage/article/2024/08/09/9e8hkh696WJM4GSt4NVwQUzvxLZaYcbs0DAnmcIZ.png)   
页面设置-卷：新建-名称-标签大小(宽度：90.0 mm，高度：60.0 mm)-裸露衬纸宽度(左：1.0mm，右：2.0mm)
![image.png](https://blog.alonesky.com/storage/article/2024/08/09/MQ3fIXgAJN733TlYvK8YcmmYg6EnkehnbQISu8k3.png)
![image.png](https://blog.alonesky.com/storage/article/2024/08/09/hr0zg7e0ucMhJcbYmAVVLzE1rCZXpRFvOJ18ATMs.png)   
选项：打印速度(50.80mm/s)
![image.png](https://blog.alonesky.com/storage/article/2024/08/09/S7LdWDMoGH0e3gXZQvwmMRV1Xcmrw6gzyw2qbxX6.png)

3. 网页设置目标打印机(TSC TTP-244 Pro)，布局(纵向)，纸张尺寸(90mm*60mm)，边距(最小值)
![image.png](https://blog.alonesky.com/storage/article/2024/08/09/NwbEOYBzKxtjzFM5xj4ZNJXWyUft3MYtfAqgXOBH.png)

4. 打印页面
```print.html 
<style type="text/css">
    /* 在元素前插入分页符 */
    .page-break-before {
        page-break-before: always;
    }
    /* 在元素后插入分页符 */
    .page-break-after {
        page-break-after: always !important;
    }
    @font-face {
        font-family: 'AlibabaPuHuiTi-2-85-Bold';
        src:
            url(/assets/fonts/AlibabaPuHuiTi-2-85-Bold.ttf) format('TrueType');
    }
    body {
        width: 100vw !important;
        height: 100vh !important;
        margin: 0 !important;
        padding: 0 !important;
        font-size: 16pt !important;
        font-weight: initial !important;
        font-family: 'AlibabaPuHuiTi-2-85-Bold' !important;
    }
    .content {
        padding-top: 0 !important;
        padding-bottom: 0 !important;
    }
    #print-form {
        margin: 0 auto !important;
    }
    .print_box {
        width: 100% !important;
        padding-top: 6pt !important;
    }
    .form-group {
        margin-bottom: 6pt !important;
    }
    .form-group div {
        margin: 0 !important;
        padding: 0 !important;
        font-weight: bolder !important;
    }
    .company_box {
        font-size: 26pt !important;
        margin-bottom: 0 !important;
    }
    .text_box {
        position: relative !important;
    }
    .text_box .form-group div.control-label {
        padding-top: 0 !important;
    }
    .qrcode_box {
        width: 100vw !important;
        position: absolute !important;
        top: 0 !important;
        left: 60vw !important;
    }
    .qrcode_box img {
        width: 110pt !important;
    }
    .desc_text {
        text-align: left !important;
        font-size: 15pt !important;
    }
</style>
<div id="print-form" class="form-horizontal">
    {volist name="product_list" id="item"}
    <div class="print_box col-xs-12 col-sm-12">
        <div class="form-group">
            <div class="company_box text-center">超级锂电产品质保码</div>
        </div>
        <div class="text_box col-xs-12 col-sm-12">
            <div class="form-group">
                <div class="text_box_text col-xs-12 col-sm-12">产品名称：{$item.product_name}</div>
            </div>
            <div class="form-group">
                <div class="text_box_text col-xs-12 col-sm-12">电芯品牌：{$item.brand.brand_name}</div>
            </div>
            <div class="form-group">
                <div class="text_box_text col-xs-12 col-sm-12">安全编号：{$item.security_no}</div>
            </div>
            <div class="form-group">
                <div class="text_box_text col-xs-12 col-sm-12">电池编码：{$item.product_no}</div>
            </div>
            <div class="qrcode_box">
                <img src="{$item.product_qrcode}">
            </div>
        </div>
        <div class="desc_box col-xs-12 col-sm-12">
            <div class="form-group">
                <div class="desc_text text-center">
                    初次使用必须扫码登录并上传资料，否则将会影响电池质保，感谢您选择XX锂电池(撕毁不保)。
                </div>
            </div>
        </div>
    </div>
    {if ($key+1)!=count($product_list) && count($product_list)>=($key+2)}
    <div class="page-break-after"></div>
    {/if}
    {/volist}
</div>
<div class="form-group layer-footer">
    <label class="control-label col-xs-12 col-sm-2"></label>
    <div class="col-xs-12 col-sm-8">
        <button type="button" class="btn btn-primary btn-close" onclick="Layer.closeAll();">{:__('Close')}</button>
        <button type="button" class="btn btn-primary btn-window-print">打印</button>
    </div>
</div>
```

> 坑点
> 1. 碳带对不齐打印机会提示错误
> 2. 浏览器页面的打印-使用系统对话框进行打印仅对本次打印有效

参考链接：

驱动程序文档：[https://www.chinatsc.cn/zh-CN/products/ttp-series-4-inch-desktop-printers#specifications](https://www.chinatsc.cn/zh-CN/products/ttp-series-4-inch-desktop-printers#specifications)