---
layout: post
title: fastadmin 使用 topthink/img 压缩、裁剪、加水印、处理图片
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

1. 安装
```
composer require topthink/think-image
```

2. 使用（application/admin/controller/Ajax.php）   
> 在fastadmin中上传文件的公用方法中使用       
> 思路：可以添加上传参数type，系统会根据type选择不同的方式处理图片    
```
$new_file_name = '';
if (in_array($fileInfo['type'], ['image/gif', 'image/jpg', 'image/jpeg', 'image/bmp', 
'image/png', 'image/webp']) || in_array($suffix, ['gif', 'jpg', 'jpeg', 'bmp', 'png', 'webp'])) {
    $image = \think\Image::open(request()->file('file'));
    $scene_type = request()->post('type');
    $new_file_name = $this->buildSaveName($fileName);
    switch ($scene_type){
        case 'product_thumb' :
            $splInfo = $image->thumb(200, 200)->save('.' . $uploadDir . $new_file_name);
            break;
        case 'product_img' :
            $splInfo = $image->thumb(750, 750)->save('.' . $uploadDir . $new_file_name);
            break;
        case 'product_detail_img' :
            $splInfo = $image->save('.' . $uploadDir . $new_file_name);
            break;
        default:
            $splInfo = $image->save('.' . $uploadDir . $new_file_name, null, 80);
            break;
    }
}else{
    $splInfo = $file->validate(['size' => $size])->move(ROOT_PATH . '/public' . $uploadDir, $fileName);
}
```

3. 其他使用    
ThinkPHP5.0 图像处理手册[https://www.kancloud.cn/manual/thinkphp5/177530](https://www.kancloud.cn/manual/thinkphp5/177530)

原文链接：[https://blog.csdn.net/sinat_37390744/article/details/120329584](https://blog.csdn.net/sinat_37390744/article/details/120329584)