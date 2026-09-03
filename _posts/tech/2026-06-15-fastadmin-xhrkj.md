---
layout: post
title: FastAdmin导入带图片表格
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

#### FastAdmin导入带图片表格
```
use Exception;
use app\admin\library\Auth;
use think\exception\PDOException;
use PhpOffice\PhpSpreadsheet\Reader\Xls;
use PhpOffice\PhpSpreadsheet\Reader\Csv;
use PhpOffice\PhpSpreadsheet\Reader\Xlsx;
use PhpOffice\PhpSpreadsheet\Cell\Coordinate;
use PhpOffice\PhpSpreadsheet\Worksheet\MemoryDrawing;

public function import()
{
    $file = $this->request->request('file');
    if (!$file) {
        $this->error(__('Parameter %s can not be empty', 'file'));
    }
    $filePath = ROOT_PATH . DS . 'public' . DS . $file;
    if (!is_file($filePath)) {
        $this->error(__('No results were found'));
    }
    //实例化reader
    $ext = pathinfo($filePath, PATHINFO_EXTENSION);
    if (!in_array($ext, ['csv', 'xls', 'xlsx'])) {
        $this->error(__('Unknown data format'));
    }
    if ($ext === 'csv') {
        $file = fopen($filePath, 'r');
        $filePath = tempnam(sys_get_temp_dir(), 'import_csv');
        $fp = fopen($filePath, 'w');
        $n = 0;
        while ($line = fgets($file)) {
            $line = rtrim($line, "\n\r\0");
            $encoding = mb_detect_encoding($line, ['utf-8', 'gbk', 'latin1', 'big5']);
            if ($encoding !== 'utf-8') {
                $line = mb_convert_encoding($line, 'utf-8', $encoding);
            }
            if ($n == 0 || preg_match('/^".*"$/', $line)) {
                fwrite($fp, $line . "\n");
            } else {
                fwrite($fp, '"' . str_replace(['"', ','], ['""', '","'], $line) . "\"\n");
            }
            $n++;
        }
        fclose($file) || fclose($fp);

        $reader = new Csv();
    } elseif ($ext === 'xls') {
        $reader = new Xls();
    } else {
        $reader = new Xlsx();
    }
    $HotelModel=new \app\admin\model\Hotel();

    //导入文件首行类型,默认是注释,如果需要使用字段名称请使用name
    $importHeadType = isset($this->importHeadType) ? $this->importHeadType : 'comment';

    $table = $this->model->getQuery()->getTable();
    $database = \think\Config::get('database.database');
    $fieldArr = [];
    $list = db()->query("SELECT COLUMN_NAME,COLUMN_COMMENT FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = ? AND TABLE_SCHEMA = ?", [$table, $database]);
    foreach ($list as $k => $v) {
        if ($importHeadType == 'comment') {
            $v['COLUMN_COMMENT'] = explode(':', $v['COLUMN_COMMENT'])[0]; //字段备注有:时截取
            $fieldArr[$v['COLUMN_COMMENT']] = $v['COLUMN_NAME'];
        } else {
            $fieldArr[$v['COLUMN_NAME']] = $v['COLUMN_NAME'];
        }
    }

    //加载文件
    $insert = [];
    try {
        if (!$PHPExcel = $reader->load($filePath)) {
            $this->error(__('Unknown data format'));
        }
        $currentSheet = $PHPExcel->getSheet(0);  //读取文件中的第一个工作表
        $allColumn = $currentSheet->getHighestDataColumn(); //取得最大的列号
        $allRow = $currentSheet->getHighestRow(); //取得一共有多少行
        $maxColumnNumber = Coordinate::columnIndexFromString($allColumn);
        // 获取 Excel 第一行的表头文字
        $fields = [];
        for ($currentRow = 1; $currentRow <= 1; $currentRow++) {
            for ($currentColumn = 1; $currentColumn <= $maxColumnNumber; $currentColumn++) {
                $val = $currentSheet->getCellByColumnAndRow($currentColumn, $currentRow)->getValue();
                $fields[$currentColumn] = $val;// 建立 列号数字 -> 表头中文 的映射
            }
        }

        // 先循环读取文本数据，并用“Excel实际行号”作为数组键名，防止行号错位
        for ($currentRow = 2; $currentRow <= $allRow; $currentRow++) {
            $values = [];
            for ($currentColumn = 1; $currentColumn <= $maxColumnNumber; $currentColumn++) {
                $val = $currentSheet->getCellByColumnAndRow($currentColumn, $currentRow)->getValue();
                $values[] = is_null($val) ? '' : $val;
            }
            // 导入时数据列表有空行，跳过空行
            if (!implode('', $values)) {
                continue;
            }
            // 将表头中文与对应行的值组合
            $temp = array_combine(array_values($fields), $values);
            $row = [];

            foreach ($temp as $k => $v) {
                if (isset($fieldArr[$k]) && $k !== '') {
                    $row[$fieldArr[$k]] = $v;
                    if ($k == '志愿者' || $k == '志愿者手机号') { 
                        $serviceId=$this->model->where(['nickname'=>$temp['志愿者'],'mobile'=>$temp['志愿者手机号']])->value('id');
                        $row[$fieldArr[$k]] = !empty($serviceId) ? $serviceId : 0;
                    }
                    if ($k == '酒店名称') { 
                        $hotelId=$HotelModel->where(['name'=>$temp['酒店名称']])->value('id');
                        $row[$fieldArr[$k]] = !empty($hotelId) ? $hotelId : 0;
                    }
                    $row['group_id'] = 2;
                }
            }
            if ($row) {
                // 【核心改动】使用 Excel 实际行号 $currentRow 作为键名，方便图片精准定位
                $insert[$currentRow] = $row;
            }
        }
        // print_r($insert);exit;
        /************* 3. 图片数据优化处理（按天归档 + MD5 去重） *****************/
        // 💡 契合 FastAdmin 规范：生成类似 /uploads/20260615/ 这样的目录
        $dateDir = date('Ymd');
        $imageSavePath = '/uploads/' . $dateDir . '/'; // 存入数据库的相对目录
        $imageFilePath = ROOT_PATH . 'public' . $imageSavePath; // 图片保存绝对目录

        if (!file_exists($imageFilePath)) {
            mkdir($imageFilePath, 0777, true);
        }

        // 用来在当前单次导入周期内建立 MD5 和文件名的缓存映射，防止单文件内重复图多次写磁盘
        $md5Cache = [];

        // 处理图片并直接注入到上面的数据中
        foreach ($currentSheet->getDrawingCollection() as $img) {
            // 1. 获取图片底层记录的原始左上角坐标（例如：D1）
            list($startColumn, $startRow) = Coordinate::coordinateFromString($img->getCoordinates());
            
            // 💡 【视觉矫正核心】
            // 如果图片判定在第一行（表头行），但在实际业务中表头是不可能放证件照的，这一定是越界了
            if ($startRow == 1) {
                // 获取图片的高度（像素）
                $imgHeight = $img->getHeight();
                // 获取第一行（表头行）的像素高度，如果未显式设置，通常默认是 20 像素左右
                $row1Height = $currentSheet->getRowDimension(1)->getRowHeight();
                $row1Height = $row1Height > 0 ? $row1Height : 20; 

                // 如果图片的总高度，远远超过了第一行剩余的空间，说明它身体的大部分在第二行
                // 我们强行把行号修正为 2
                if ($imgHeight > $row1Height) {
                    $startRow = 2;
                }
            }

            // 2. 更加强壮的通用中心点检查（可选）：如果行号大于1，也可以通过图片的中心点落在哪一行来判定
            // 这里我们先针对你的痛点：只要卡在第1行的图，一律视为第2行的图
            if ($startRow == 1 && $img->getCoordinates() !== 'D1') {
                // 如果其他列（非证件照列）也误触了，可以加具体的列名判断
            }

            // 校验这一行数据在文本数组里是否存在
            if (!isset($insert[$startRow])) {
                continue;
            }

            $currentColumnNum = Coordinate::columnIndexFromString($startColumn);
            $headerTitle = isset($fields[$currentColumnNum]) ? $fields[$currentColumnNum] : '';
            $dbFieldName = isset($fieldArr[$headerTitle]) ? $fieldArr[$headerTitle] : '';

            if (!$dbFieldName) {
                continue; 
            }

            $fileContent = null;
            $extension = '.png'; 

            try {
                if (method_exists($img, 'getImageResource')) {
                    $imageResource = $img->getImageResource();
                    if ($imageResource !== false && (is_resource($imageResource) || $imageResource instanceof \GdImage)) {
                        ob_start();
                        imagepng($imageResource);
                        $fileContent = ob_get_contents();
                        ob_end_clean();
                    }
                }
                
                if (is_null($fileContent) && !$img instanceof MemoryDrawing && method_exists($img, 'getPath')) {
                    $path = $img->getPath();
                    if (strpos($path, 'zip://') === 0) {
                        $fileContent = @file_get_contents($path);
                    } else {
                        if (file_exists($path)) {
                            $fileContent = @file_get_contents($path);
                        }
                    }
                }
            } catch (Exception $e) {
                continue; 
            }

            // 保存文件并合并到数组
            if (!is_null($fileContent) && $fileContent !== '') {
                $fileMd5 = md5($fileContent);
                $imageFileName = $fileMd5 . $extension;
                $fullPath = $imageSavePath . $imageFileName;

                if (isset($md5Cache[$fileMd5]) || file_exists($imageFilePath . $imageFileName)) {
                    $isSaved = true;
                } else {
                    $isSaved = @file_put_contents($imageFilePath . $imageFileName, $fileContent);
                    if ($isSaved) {
                        $md5Cache[$fileMd5] = $fullPath;
                    }
                }

                if ($isSaved) {
                    // 此时的 $startRow 已经是经过上面智能矫正后的真实行号（2）了！
                    if (isset($insert[$startRow][$dbFieldName]) && $insert[$startRow][$dbFieldName] !== '') {
                        if (strpos($insert[$startRow][$dbFieldName], $fullPath) === false) {
                            $insert[$startRow][$dbFieldName] .= ',' . $fullPath;
                        }
                    } else {
                        $insert[$startRow][$dbFieldName] = $fullPath;
                    }
                }
            }
        }

        // 【核心重置】将具有不连续行号键名的数组，重置为标准的 0,1,2... 索引数组，供后面批量保存
        $insert = array_values($insert);
    } catch (Exception $exception) {
        $this->error($exception->getMessage());
    }
    if (!$insert) {
        $this->error(__('No rows were updated'));
    }
    // print_r($insert);exit;
    try {
        //是否包含admin_id字段
        $has_admin_id = false;
        foreach ($fieldArr as $name => $key) {
            if ($key == 'admin_id') {
                $has_admin_id = true;
                break;
            }
        }
        if ($has_admin_id) {
            $auth = Auth::instance();
            foreach ($insert as &$val) {
                if (empty($val['admin_id'])) {
                    $val['admin_id'] = $auth->isLogin() ? $auth->id : 0;
                }
            }
        }
        $this->model->saveAll($insert);
    } catch (PDOException $exception) {
        $msg = $exception->getMessage();
        if (preg_match("/.+Integrity constraint violation: 1062 Duplicate entry '(.+)' for key '(.+)'/is", $msg, $matches)) {
            $msg = "导入失败，包含【{$matches[1]}】的记录已存在";
        }
        $this->error($msg);
    } catch (Exception $e) {
        $this->error($e->getMessage());
    }

    $this->success();
}
```