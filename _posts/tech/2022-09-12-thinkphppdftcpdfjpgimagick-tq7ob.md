---
layout: post
title: ThinkPHP生成PDF文档(TCPDF)并转为JPG(Imagick)
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

安装 TCPDF

```
composer require tecnickcom/tcpdf
```

```
use Imagick;
use TCPDF;
/**
 * 生成PDF文档并转为jpg
 * @param int $task_id 任务Id
 * @return string
 * @throws \think\db\exception\DataNotFoundException
 * @throws \think\db\exception\ModelNotFoundException
 * @throws \think\exception\DbException
 */
public function generateReports($task_id = 1)
{
    $taskInfo = $this->getTaskInfo($task_id);
    if (!empty($taskInfo['report_url']) && 0) {
        $report_url = $taskInfo['report_url'];
    } else {
        $taskLogList = $taskInfo['taskLogList'];
        foreach ($taskLogList as $key => $item) {
            if (empty($item['list']) || count($item['list']) < 2) {
                unset($taskLogList[$key]);
            }
            if (!empty($item['list'][1]['sign_time'])) {
                $hours = (($item['list'][1]['sign_time'] - $item['list'][0]['sign_time']) / 3600);
                if ($hours < 2) {
                    unset($taskLogList[$key]);
                }
            } else {
                unset($taskLogList[$key]);
            }
        }
        $taskLogList = array_values($taskLogList);
        $taskLogCount = count($taskLogList);
        $pdf = new TCPDF(PDF_PAGE_ORIENTATION, PDF_UNIT, PDF_PAGE_FORMAT, true, 'UTF-8', false);
        // 设置文件信息
        $pdf->SetCreator(PDF_CREATOR);
        $pdf->SetAuthor("ZhiShun");
        $pdf->SetTitle("参与社会公益服务工作记录表");
        $pdf->SetSubject('ZhiShun');
        $pdf->SetKeywords('ZhiShun, PDF, 参与社会公益服务工作记录表');
        // 删除预定义的打印 页眉/页尾
        $pdf->setPrintHeader(false);
        $pdf->setPrintFooter(false);
        // 设置默认等宽字体
        $pdf->SetDefaultMonospacedFont(PDF_FONT_MONOSPACED);
        $pdf->SetMargins(PDF_MARGIN_LEFT, 0, PDF_MARGIN_RIGHT);
        $pdf->setCellPaddings(0, 0, 0, 0);
        $pdf->SetLineStyle(['width' => 0, 'color' => [255, 255, 255]]);
        // 设置自动分页符
        $pdf->SetFont('zh_cnhp15cn', 'I', 20);
        $pdf->AddPage();
        $pdf->SetAutoPageBreak(true);
        $pdf->Cell(0, 30, '服务工作记录表', 0, 1, 'C');
        $pdf->SetFont('zh_cnhp15cn', 'I', 16);
        $pdf->Cell(0, 20, '姓名：' . $taskInfo['service']['username'] . '   身份证号码：' . $taskInfo['service']['idcard'] .
            '   联系电话：' . $taskInfo['service']['mobile'], 0, 1, 'C');
        // 设置边框颜色
        $pdf->SetDrawColor(0, 0, 0);
        $pdf->SetLineWidth(0.1);
        $pdf->Cell(36, 7, '服务类型', 1, 0, 'C');
        $pdf->Cell(36, 7, '日期', 1, 0, 'C');
        $pdf->Cell(55, 7, '地点', 1, 0, 'C');
        $pdf->Cell(17, 7, '时长', 1, 0, 'C');
        $pdf->Cell(36, 7, '见证人', 1, 0, 'C');
        // dump(json_decode(json_encode($taskLogList)));exit;
        if (!empty($taskLogList)) {
            if($taskLogCount<5) {
                $pdf->Ln();
                $pdf->Cell(36, 60, '', 1, 0, 'C');
                $pdf->Ln(0, true);
                $pdf->Cell(36, 52, '道路交通', 0, 0, 'C');
                $pdf->Ln(12, true);
                $pdf->Cell(36, 52, '安全劝导', 0, 0, 'C');
            }else{
                $pdf->Ln();
                $pdf->Cell(36, 12*$taskLogCount, '', 1, 0, 'C');
                $pdf->Ln(0, true);
                $pdf->Cell(36, 12*$taskLogCount-8, '道路交通', 0, 0, 'C');
                $pdf->Ln(12, true);
                $pdf->Cell(36, 12*$taskLogCount-8, '安全劝导', 0, 0, 'C');
            }
            foreach ($taskLogList as $key => $item) {
                if (!empty($item['list'][1]['sign_time'])) {
                    $hours = (($item['list'][1]['sign_time'] - $item['list'][0]['sign_time']) / 3600);
                    if ($hours >= 2) {
                        $hours = '2小时';
                    } else {
                        $hours = '打卡时间不足,无法计算';
                    }
                } else {
                    $hours = '缺勤,无法计算';
                }
                if ($key == 0) {
                    $pdf->Ln(-12, true);
                    $pdf->Cell(36, 12, '', 0, 0, 'C');
                    // 日期
                    $pdf->Cell(36, 12, $item['label'], 1, 0, 'C', false, '', 1);
                    // 地点
                    $pdf->Cell(55, 12, $item['list'][0]['place_text'], 1, 0, 'C', false, '', 1);
                    // 时长
                    $pdf->Cell(17, 12, $hours, 1, 0, 'C', false, '', 1);
                    // 见证人
                    $pdf->Cell(36, 12, $taskInfo['traffic_text'], 1, 0, 'C', false, '', 1);
                } else {
                    $pdf->Ln(12, true);
                    $pdf->Cell(36, 12, '', 0, 0, 'C');
                    $pdf->Cell(36, 12, $item['label'], 1, 0, 'C', false, '', 1);
                    $pdf->Cell(55, 12, $item['list'][0]['place_text'], 1, 0, 'C', false, '', 1);
                    $pdf->Cell(17, 12, $hours, 1, 0, 'C', false, '', 1);
                    $pdf->Cell(36, 12, $taskInfo['traffic_text'], 1, 0, 'C', false, '', 1);
                }
            }
            if($taskLogCount<5){
                for ($i=0;$i<(5-$taskLogCount);$i++){
                    $pdf->Ln(12, true);
                    $pdf->Cell(36, 12, '', 0, 0, 'C');
                    // 日期
                    $pdf->Cell(36, 12, '', 1, 0, 'C', false, '', 1);
                    // 地点
                    $pdf->Cell(55, 12, '', 1, 0, 'C', false, '', 1);
                    // 时长
                    $pdf->Cell(17, 12, '', 1, 0, 'C', false, '', 1);
                    // 见证人
                    $pdf->Cell(36, 12, '', 1, 0, 'C', false, '', 1);
                }
            }

        } else {
            $pdf->Ln();
            $pdf->Cell(36, 60, '', 1, 0, 'C');
            $pdf->Ln(0, true);
            $pdf->Cell(36, 52, '道路交通', 0, 0, 'C');
            $pdf->Ln(12, true);
            $pdf->Cell(36, 52, '安全劝导', 0, 0, 'C');
            for ($i=0;$i<5;$i++){
                if ($i == 0) {
                    $pdf->Ln(-12, true);
                }else{
                    $pdf->Ln(12, true);
                }
                $pdf->Cell(36, 12, '', 0, 0, 'C');
                // 日期
                $pdf->Cell(36, 12, '', 1, 0, 'C', false, '', 1);
                // 地点
                $pdf->Cell(55, 12, '', 1, 0, 'C', false, '', 1);
                // 时长
                $pdf->Cell(17, 12, '', 1, 0, 'C', false, '', 1);
                // 见证人
                $pdf->Cell(36, 12, '', 1, 0, 'C', false, '', 1);
            }
        }
        $pdf->Ln();
        $pdf->Cell(36, 12, '接受安全教育', 1, 0, 'C', false, '', 1);
        $pdf->Cell(36, 12, '', 1, 0, 'C');
        $pdf->Cell(55, 12, '', 1, 0, 'C');
        $pdf->Cell(17, 12, '', 1, 0, 'C');
        $pdf->Cell(36, 12, '', 1, 0, 'C');
        $pdf->Ln();
        $pdf->Cell(180, 50, '', 1, 0, 'C');
        $pdf->Ln(0, true);
        $pdf->Cell(30, 15, '评价：', 0, 0, 'C');
        $pdf->Cell(10, 30, '好', 0, 0, 'C');
        $pdf->Cell(30, 30, '良好', 0, 0, 'C');
        $pdf->Cell(30, 30, '合格', 0, 0, 'C');
        $pdf->Cell(30, 30, '不合格', 0, 0, 'C');
        $pdf->Ln(30, true);
        $pdf->Cell(150, 15, '年  月   日', 0, 0, 'R');
        $pdf->Ln(7, true);
        $pdf->Cell(145, 15, '（印章）', 0, 0, 'R');
        $pdf->Ln(22, true);
        $pdf->Cell(20, 10, '备注：', 0, 0, 'L');
        $pdf->Ln();
        $pdf->Cell(150, 10, '1、自    年   月   日起3个工作日内到交警部门报到。', 0, 0, 'L');
        $pdf->Ln();
        $pdf->Cell(150, 10, '2、公益服务时长不少于5/7/9日，每天不少于2个小时,保证在15个工作日', 0, 0, 'L');
        $pdf->Ln();
        $pdf->Cell(150, 10, '内完成。自  年   月   日   至   年   月   日。', 0, 0, 'L');
        $pdf->Ln();
        $pdf->Cell(150, 10, '3、参与社会公益活动后，积极撰写参与公益活动的心得体会或悔过书， ', 0, 0, 'L');
        $pdf->Ln();
        $pdf->Cell(150, 10, '并提交检察机关。  ', 0, 0, 'L');
        $pdf->Ln();
        $pdf->Cell(150, 10, '4、报到地点及联系方式：', 0, 0, 'L');
        $pdf->Ln();
        $pdf->Cell(150, 10, '交警一大队：交警直属一大队事故处理中队', 0, 0, 'L');
        $pdf->Ln();
        $pdf->Cell(150, 10, '211办公室', 0, 0, 'L');
        $pdf->Ln();
        $pdf->Cell(150, 10, '警官 0000--0000000、0000000', 0, 0, 'C');
        $report_url = ROOT_PATH . 'public/report/task_' . $task_id . $taskInfo['service_id'] . $taskInfo['traffic_id'] . $taskInfo['place_id'] . $taskInfo['user_id'];
        $pdf->Output($report_url . ".pdf", "F");
        $this->pdf2image($report_url . ".pdf", $report_url);
        $report_url = str_replace(ROOT_PATH . 'public', '', $report_url) . '.jpeg';
        $this->where('id', $task_id)->setField('report_url', $report_url);
    }
    return getFileFullUrl($report_url);
}
/**
 * PDF2Image
 * @param string $pdf 待处理的PDF文件
 * @param string $path 待保存的图片路径
 * @param int $page 待导出的页面 -1为全部 0为第一页 1为第二页
 * @return   string   保存好的图片路径和文件名
 */
function pdf2image($pdf, $path, $page = -1)
{
    $fileArray = [];
    // dump(extension_loaded('Imagick'));exit;
    if (!extension_loaded('Imagick')) {
        $this->error = 'Imagick扩展不存在';
        return false;
    }
    if (!file_exists($pdf)) {
        $this->error = dirname($pdf) . '文件不存在';
        return false;
    }
    $images = new Imagick();
    $images->setResolution(300, 300);
    $images->setCompressionQuality(100);
    try {
        if ($page == -1) {
            $images->readImage($pdf);
        } else {
            $images->readImage($pdf . "[" . $page . "]");
        }
    } catch (\Exception $e) {
        write_log($e->getMessage());
    }
    foreach ($images as $key => $item) {
        $item->setImageFormat('jpeg');
        $item->setImageCompression(Imagick::COMPRESSION_JPEG);
        $item->setImageCompressionQuality(100);
        if (count($images) == 1) {
            $filename = $path . '.jpeg';
        } else {
            $filename = $path . '_' . $key . '.jpeg';
        }
        $result = $item->writeImage($filename);
        if ($result == true) {
            $fileArray[] = $filename;
        } else {
            write_log($result);
        }
    }
    if (count($fileArray) != 1) {
        $this->spliceImg($fileArray, $path);
    } else {
        return $fileArray[0];
    }
}

/**
 * 合并图片
 * @param array $images 图片数组
 * @param string $filename 文件名
 * @return string  将多个图片拼接为成图的路径
 * 注：多页的pdf转化为图片后拼接方法
 */
function spliceImg($images = [], $filename = '')
{
    ini_set('memory_limit', '1G');
    //自定义宽度
    $width = 2480;
    //获取总高度
    $pic_tall = 0;
    foreach ($images as $key => $value) {
        $info = getimagesize($value);
        $pic_tall += $width / $info[0] * $info[1];
    }
    // 创建长图
    $temp = imagecreatetruecolor($width, $pic_tall);
    //分配一个白色底色
    $color = imagecolorAllocate($temp, 255, 255, 255);
    imagefill($temp, 0, 0, $color);
    $target_img = $temp;
    $source = array();
    foreach ($images as $k => $v) {
        $source[$k]['source'] = Imagecreatefromjpeg($v);
        $source[$k]['size'] = getimagesize($v);
    }
    $tmp = 1;
    $tmpy = 0; //图片之间的间距
    $count = count($images);
    for ($i = 0; $i < $count; $i++) {
        imagecopy($target_img, $source[$i]['source'], $tmp, $tmpy, 0, 0, $source[$i]['size'][0], $source[$i]['size'][1]);
        $tmpy = $tmpy + $source[$i]['size'][1];
        //释放资源内存
        imagedestroy($source[$i]['source']);
    }
    $return_imgpath = $filename . '.jpeg';
    $result = imagejpeg($target_img, $return_imgpath);
    if (!empty($result)) {
        foreach ($images as $key => $value) {
            @unlink($value);
            @unlink($filename.'.pdf');
        }
    }
    return $return_imgpath;
}
/**
 * 获取文件完整连接
 * @param string $value
 * @return string
 */
function getFileFullUrl($value = '')
{
    if (stripos($value, 'http') === 0 || $value === '' || stripos($value, 'data:image') === 0) {
        return $value;
    } else {
        $upload = \think\Config::get('upload');
        if (!empty($upload['cdnurl'])) {
            return $upload['cdnurl'] . $value;
        } else {
            $http_type = ((isset($_SERVER['HTTPS']) && $_SERVER['HTTPS'] == 'on') || (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] == 'https')) ? 'https://' : 'http://';
            return $http_type . $_SERVER['HTTP_HOST'] . $value;
        }
    }
}
```
参考链接：

[https://www.cnblogs.com/relix/p/4982919.html](https://www.cnblogs.com/relix/p/4982919.html)

[https://www.kancloud.cn/potatog/tcpdf/1358504](https://www.kancloud.cn/potatog/tcpdf/1358504)

[https://www.jb51.net/article/60337.htm](https://www.jb51.net/article/60337.htm)

[https://www.lmonkey.com/t/wDExaleyK](https://www.lmonkey.com/t/wDExaleyK)