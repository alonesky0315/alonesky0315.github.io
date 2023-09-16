---
layout: post
title: DcataAdmin导入Excel(包含图片)
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

> DcatAdmin 利用 phpoffice/phpspreadsheet 导入包含图片的数据   

1. 安装phpoffice/phpspreadsheet   
```
composer require phpoffice/phpspreadsheet
```

2. 控制器添加自定义工具栏按钮   
```
// 引入导出工具按钮
$grid->tools(function (Grid\Tools $tools) use ($grid) {
    // 导入
    $tools->append(Modal::make()
        // 大号弹窗
        ->lg()
        // 弹窗标题
        ->title('上传文件')
        // 按钮
        ->button('<button class="btn btn-primary">
				<i class="feather icon-upload"></i> 导入数据</button>')
        // 弹窗内容
        ->body(MaintenanceImport::make()));
    // 下载导入模板
    $tools->append('<a href="/storage/files/maintenanceImportTemplate.xlsx" 
		download="maintenanceImportTemplate.xlsx" target="_blank" 
		class="btn btn-primary btn-outline"><i class="feather icon-download">
		</i>下载模板</a>');
});
```

3. 创建Form弹窗MaintenanceImport.php   

```MaintenanceImport.php
<?php

namespace App\Admin\Forms;

use Dcat\Admin\Widgets\Form;
use Illuminate\Support\Facades\DB;
use PhpOffice\PhpSpreadsheet\Cell\Coordinate;
use PhpOffice\PhpSpreadsheet\IOFactory;

class MaintenanceImport extends Form
{
    public function handle(array $input)
    {
        try {
            //上传文件位置，这里默认是在storage中，如有修改请对应替换
            $file_path = storage_path('app/public/' . $input['import_file']);
            $imageFilePath = public_path('upload/images/');
            if (!file_exists($imageFilePath)) { //如果目录不存在则递归创建
                mkdir($imageFilePath, 0777, true);
            }
            $reader = IOFactory::createReader('Xlsx');
            $spreadsheet = $reader->load($file_path);
            $excelData = $spreadsheet->getSheet(0);
            $excelList = $excelData->toArray();
            // print_r($excelList);exit;
            $imageData = [];
            if (!empty($excelList)) {
                foreach ($excelData->getDrawingCollection() as $drawing) {
                    list($startColumn, $startRow) = Coordinate::coordinateFromString($drawing->getCoordinates());
                    $imageFileName = md5(time());
                    switch ($drawing->getExtension()) {
                        case 'jpg':
                        case 'jpeg':
                            $imageFileName .= '.jpg';
                            $source = imagecreatefromjpeg($drawing->getPath());
                            imagejpeg($source, $imageFilePath . $imageFileName);
                            break;
                        case 'gif':
                            $imageFileName .= '.gif';
                            $source = imagecreatefromgif($drawing->getPath());
                            imagegif($source, $imageFilePath . $imageFileName);
                            break;
                        case 'png':
                            $imageFileName .= '.png';
                            $source = imagecreatefrompng($drawing->getPath());
                            imagepng($source, $imageFilePath . $imageFileName);
                            break;
                    }
                    $imageData[$startRow - 1][$startColumn] = 'upload/images/' . 
										$imageFileName;
                }
                if (!empty($imageData)) {
                    foreach ($imageData as $key => $value) {
                        // $data[$key][2] 是导入表格图片的哪一行
                        $excelList[$key][1] = $value['B'];
                        $excelList[$key][3] = $value['D'];
                        $excelList[$key][4] = $value['E'];
                    }
                }
                for ($i = 1; $i < count($excelList); $i++) {
                    $findUser = DB::table('user')
										->where('maintenance_phone', $excelList[$i][2])
										->value('id');
                    if (!empty($findUser)) {
                        continue;
                    }
                    $insertData[$i] = [
                        'name' => $excelList[$i][0],
                        'maintenance_avatar' => $excelList[$i][1],
                        'phone' => $excelList[$i][2],
                        'maintenance_phone' => $excelList[$i][2],
                        'image' => $excelList[$i][3],
                        'images' => $excelList[$i][4],
                        'type' => 1,
                        'is_maintenance' => 1,
                        'maintenance_status' => 1,
                        // 'province'=>$excelList[$i],
                        // 'city'=>$excelList[$i],
                        // 'area'=>$excelList[$i],
                        // 'maintenance_ids'=>$excelList[$i],
                        // 'maintenance_status'=>$excelList[$i],
                        'time' => date('Y-m-d H:i', strtotime($excelList[$i][5]))
                    ];
                }
                // print_r($insertData);exit;
                if (!empty($insertData)) {
                    DB::table('user')->insert($insertData);
                }
            }
            return $this->response()->success('数据导入成功')->refresh();
        } catch (\Exception $e) {
            return $this->response()->error($e->getMessage());
        }
    }
    public function form()
    {
        // 禁用重置表单按钮
        $this->disableResetButton();
        // 文件上传
        $this->file('import_file', ' ')
            ->disk('public')
            ->accept('xlsx')
            ->uniqueName()
            ->autoUpload()
            ->move('/import')
            ->help('支持xlsx');
    }
}
```

#### 备注 xls格式读取不到图片

参考链接：

DcatAdmin 简单实现导入Excel：[https://learnku.com/articles/67058](https://learnku.com/articles/67058)

phpoffice/phpspreadsheet读取Excel表格中的图片并保存到本地：[https://blog.csdn.net/weixin_44888397/article/details/131524676](https://blog.csdn.net/weixin_44888397/article/details/131524676)