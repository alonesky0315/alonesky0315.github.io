---
layout: post
title: Phpoffice/PhpSpreadsheet 导出Excel(包含图片)
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

> DcatAdmin利用Phpoffice/PhpSpreadsheet导出带图片的excel表格
 
1. 准备   
1.1 环境   
Laravel 版本：Laravel Framework 7.30.4   
PHP 版本：PHP 7.4.7 (cli)   
1.2 扩展包   
dcatAdmin 的扩展，文档链接：[传送](https://learnku.com/docs/dcat-admin/2.x)
```
composer require dcat/laravel-admin
```
phpoffice/PhpSpreadsheet 的扩展，文档链接：[传送](https://phpspreadsheet.readthedocs.io/en/latest)
```
composer require phpoffice/PhpSpreadsheet
```

2. 代码实现   
2.1 新增 GridTool 文件(二选一)   
2.1.1 命令生成   
```
php artisan admin:action
```
然后选择 [3] grid-tool 设置 grid-tool 的文件名MaintenanceExportTool
```MaintenanceExportTool文件内容
 /**
	* 接收参数
	* @param $title
	*/
 public function __construct($title = '')
 {
		 parent::__construct($title);
		 $this->title = $title;
 }

 /**
	* @return string
	*/
 protected $title = '导出';

 /**
	*  按钮的样式 * @var string
	*/
 protected $style = 'btn btn-outline-info';

 /**
	*  业务处理 * @param Request $request
	* @return Response
	*/
 public function handle(Request $request)
 {
		 // 这里有一个 admin_route/admin_url 路由，接下来在 route.php 文件添加
		 return $this->response()->download(admin_route('maintenance_export', [
				 'filename' => $request->get('filename'),
		 ]);
 }
```
2.1.2 控制器直接写   
```
// 引入导出工具按钮
$grid->tools(function (Grid\Tools $tools) use ($grid) {
		// 导出
		$tools->append('<a href="maintenance_export" target="_blank" class="btn btn-primary btn-outline"><i class="feather icon-download"></i>导出数据</a>');
});
```
2.2 新增控制器方法export   
```
/**
 *  维修人员导出
 * @param Request $request
 * @return BinaryFileResponse
**/
public function export()
{
    $maintenanceList = DB::table('user')
    ->where('type', '=', 3)
    ->whereNull('deleted_at')
    ->get(['id', 'name', 'avatar', 'phone', 'image', 'images', 'province', 'city', 'area', 'maintenance_ids', 'maintenance_status', 'time']);
    $spreadsheet = new Spreadsheet();
    $sheet = $spreadsheet->getActiveSheet();
    //设置sheet的名字  两种方法
    $sheet->setTitle('维修人员');
    //设置第一行小标题
    $k = 1;
    $sheet->setCellValue('A' . $k, '编号');
    $sheet->setCellValue('B' . $k,'姓名');
    $sheet->setCellValue('C' . $k,'头像');
    $sheet->setCellValue('D' . $k,'手机号');
    $sheet->setCellValue('E' . $k,'身份证(正)');
    $sheet->setCellValue('F' . $k,'身份证(反)');
    $sheet->setCellValue('G' . $k,'常驻地址');
    $sheet->setCellValue('H' . $k,'维修类型');
    $sheet->setCellValue('I' . $k,'维修人员状态');
    $sheet->setCellValue('J' . $k,'注册时间');

    // 设置个表格宽度
    $sheet->getColumnDimension('A')->setWidth(10);
    $sheet->getColumnDimension('B')->setWidth(15);
    $sheet->getColumnDimension('C')->setWidth(30);
    $sheet->getColumnDimension('D')->setWidth(20);
    $sheet->getColumnDimension('E')->setWidth(30);
    $sheet->getColumnDimension('F')->setWidth(30);
    $sheet->getColumnDimension('G')->setWidth(50);
    $sheet->getColumnDimension('H')->setWidth(30);
    $sheet->getColumnDimension('I')->setWidth(15);
    $sheet->getColumnDimension('J')->setWidth(20);

    // 垂直居中
    $styleArray = [
        // 'font' => [
        //     'bold' => true
        // ],
        'alignment' => [
            'horizontal' => \PhpOffice\PhpSpreadsheet\Style\Alignment::HORIZONTAL_CENTER,
        ],
    ];
    $sheet->getStyle('A1:J1')->applyFromArray($styleArray)->getFont()->setSize(12);

    //  循环赋值
    $k = 2;
    foreach ($maintenanceList as $key => $value) {
        if(!empty($value->province)){
            $province=DB::table('area')->where('code',$value->province)->value('name');
        }else{
            $province='';
        }
        if(!empty($value->city)){
            $city=Db::table('area')->where(['code'=>$value->city])->value('name');
        }else{
            $city='';
        }
        if(!empty($value->area)){
            $area=Db::table('area')->where(['code'=>$value->area])->value('name');
        }else{
            $area='';
        }
        $address=(!empty($province)?$province:'').(!empty($city)?$city:'').(!empty($area)?$area:'').(!empty($value->address)?$value->address:'');
        if(!empty($value->maintenance_ids)){
            $maintenance_type=Db::table('maintenance_type')->whereIn('id',explode(',',$value->maintenance_ids))->pluck('name')->toArray();
            $maintenance_types=implode('/',$maintenance_type);
        }else{
            $maintenance_types='';
        }
        $maintenance_status=($value->maintenance_status==0)?'待审核':($value->maintenance_status==1?'审核通过':($value->maintenance_status==2?'审核拒绝':'禁用'));
        $sheet->setCellValue('A' . $k, $value->id);
        $sheet->setCellValue('B' . $k, $value->name);
        if (!empty($value->avatar) && !empty(pathinfo($value->avatar)['basename']) && file_exists($value->avatar)) { //过滤非文件类型
            $drawing[$k] = new Drawing();
            $drawing[$k]->setName('头像');
            $drawing[$k]->setDescription('头像');
            $drawing[$k]->setPath(public_path($value->avatar));
            // $drawing[$k]->setWidth(80);
            $drawing[$k]->setHeight(80);
            $drawing[$k]->setCoordinates('C'.$k);
            $drawing[$k]->setOffsetX(12);
            $drawing[$k]->setOffsetY(12);
            $drawing[$k]->setWorksheet($spreadsheet->getActiveSheet());
        } else {
            $sheet->setCellValue('C' . $k, '');
        }
        $sheet->setCellValue('D' . $k, $value->phone);
        if (!empty($value->image) && !empty(pathinfo($value->avatar)['basename'])) { //过滤非文件类型
            $drawing[$k] = new Drawing();
            $drawing[$k]->setName('头像');
            $drawing[$k]->setDescription('头像');
            $drawing[$k]->setPath(public_path($value->avatar));
            // $drawing[$k]->setWidth(80);
            $drawing[$k]->setHeight(80);
            $drawing[$k]->setCoordinates('E'.$k);
            $drawing[$k]->setOffsetX(12);
            $drawing[$k]->setOffsetY(12);
            $drawing[$k]->setWorksheet($spreadsheet->getActiveSheet());
        } else {
            $sheet->setCellValue('E' . $k, '');
        }
        if (!empty($value->images) && !empty(pathinfo($value->avatar)['basename'])) { //过滤非文件类型
            $drawing[$k] = new Drawing();
            $drawing[$k]->setName('头像');
            $drawing[$k]->setDescription('头像');
            $drawing[$k]->setPath(public_path($value->avatar));
            // $drawing[$k]->setWidth(80);
            $drawing[$k]->setHeight(80);
            $drawing[$k]->setCoordinates('F'.$k);
            $drawing[$k]->setOffsetX(12);
            $drawing[$k]->setOffsetY(12);
            $drawing[$k]->setWorksheet($spreadsheet->getActiveSheet());
        } else {
            $sheet->setCellValue('F' . $k, '');
        }
        $sheet->setCellValue('G' . $k, $address);
        $sheet->setCellValue('H' . $k, $maintenance_types);
        $sheet->setCellValue('I' . $k, $maintenance_status);
        $sheet->setCellValue('J' . $k, $value->time);
        $sheet->getRowDimension($k)->setRowHeight(80);
        $k++;
    }
    $styleArrayBody = [
        // 'borders' => [
        //     'allBorders' => [
        //         'borderStyle' => \PhpOffice\PhpSpreadsheet\Style\Border::BORDER_THIN,
        //         'color' => ['argb' => '666666'],
        //     ],
        // ],
        'alignment' => [
            'horizontal' => \PhpOffice\PhpSpreadsheet\Style\Alignment::HORIZONTAL_CENTER,
            'vertical' => \PhpOffice\PhpSpreadsheet\Style\Alignment::VERTICAL_CENTER,
        ],
    ];
    $total_rows = count($maintenanceList) + 1;
    //添加所有边框/居中
    $sheet->getStyle('A1:J' . $total_rows)->applyFromArray($styleArrayBody);
    $sheet->getStyle('D1:D' . $total_rows)->getNumberFormat()->setFormatCode(\PhpOffice\PhpSpreadsheet\Style\NumberFormat::FORMAT_NUMBER);
    $file_name = '维修人员-'.date('YmdHi');
    //  第一种保存方式
    // $writer = new Xlsx($spreadsheet);
    // 保存的路径可自行设置
    // $file_name = '../'.$file_name . ".xlsx";
    // $writer->save($file_name);
    // 第二种直接页面上显示下载
    $file_name = $file_name . ".xls";
    header('Content-Type: application/vnd.ms-excel');
    header('Content-Disposition: attachment;filename="' . $file_name . '"');
    header('Cache-Control: max-age=0');
    $writer = IOFactory::createWriter($spreadsheet, 'Xls');
    // 注意createWriter($spreadsheet, 'Xls') 第二个参数首字母必须大写
    $writer->save('php://output');
}
```
2.3 新增控制器路由   
备注：单个方法的路由一定要放到 resource 资源路由前面   
```
$router->get('maintenance_export', 'MaintenanceController@export');
 ```
 
参考链接：

maatwebsite/excel 导出图片到 Excel：[https://learnku.com/articles/66362](https://learnku.com/articles/66362)

PhpSpreadsheet 导出图片到 Excel：[https://learnku.com/articles/26965](https://learnku.com/articles/26965)