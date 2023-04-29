---
layout: post
title: ThinkPHP生成Word文档(PHPWord)
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

安装扩展

```
composer require phpoffice/phpword
composer require tecnickcom/tcpdf
```
```
use PhpOffice\PhpWord\IOFactory;
use PhpOffice\PhpWord\PhpWord;
use PhpOffice\PhpWord\SimpleType\Jc;
use PhpOffice\PhpWord\Settings;

/**
 * 生成word文档
 * @param int $task_id 任务Id
 * @return string
 * @throws \PhpOffice\PhpWord\Exception\Exception
 * @throws \think\db\exception\DataNotFoundException
 * @throws \think\db\exception\ModelNotFoundException
 * @throws \think\exception\DbException
 */
public function generateReport($task_id = 1)
{
    $taskInfo = $this->getTaskInfo($task_id);
    if (!empty($taskInfo['report_url'])) {
        $report_url = $taskInfo['report_url'];
    } else {
        $phpWord = new PhpWord();
        // 全局设置
        // 设置字体
        $phpWord->setDefaultFontName('zh_cnhp15cn');
        // 设置字号
        $phpWord->setDefaultFontSize(12);
        // 文本水平居中
        $center = ['alignment' => Jc::CENTER];
        // 垂直居中
        $cellCenter = ['valign' => 'center'];
        // 备注样式
        $noteStyle = ['name' => 'zh_cnhp15cn', 'size' => 16];
        $noteListStyle = ['name' => 'zh_cnhp15cn', 'size' => 16, 'lineHeight' => 1.5];
        // 添加页面
        $section = $phpWord->addSection();
        // 添加空行
        $section->addTextBreak(2);
        $section->addText('服务工作记录表', ['name' => 'zh_cnhp15cn', 'size' => 22], $center);
        $section->addTextBreak(2);
        $section->addText('姓名：' . $taskInfo['service']['username'] . '   身份证号码：' . $taskInfo['service']['idcard'] .
            '   联系电话：' . $taskInfo['service']['mobile'], null, ['indent' => 0.7]);
        $section->addTextBreak();
        $table = $section->addTable(['borderSize' => 1]);
        $table->addRow(500);
        $table->addCell(2000, $cellCenter)->addText("服务类型", null, $center);
        $table->addCell(1400, $cellCenter)->addText("日期", null, $center);
        $table->addCell(3000, $cellCenter)->addText("地点", null, $center);
        $table->addCell(1200, $cellCenter)->addText("时长", null, $center);
        $table->addCell(2000, $cellCenter)->addText("见证人", null, $center);
        // 合并行
        $cellRowMerge = ['vMerge' => 'restart', 'valign' => 'center'];
        $cellRowContinue = ['vMerge' => 'continue'];
        // 合并列
        $cellColMerge = ['gridSpan' => 5];
        $table->addRow(600);
        $mergeCell = $table->addCell(2000, $cellRowMerge);
        $mergeCell->addText('道路交通', null, $center);
        $mergeCell->addText('安全劝导', null, $center);
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
        // dump(json_decode(json_encode($taskLogList)));exit;
        if (!empty($taskLogList)) {
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
                    // 日期
                    $table->addCell(1400, $cellRowMerge)->addText($item['label'], null, $center);
                    // 地点
                    $table->addCell(3000, $cellRowMerge)->addText($item['list'][0]['place_text'], null, $center);
                    // 时长
                    $table->addCell(1200, $cellRowMerge)->addText($hours, null, $center);
                    // 见证人
                    $table->addCell(2000, $cellRowMerge)->addText($taskInfo['traffic_text'], null, $center);
                } else {
                    $table->addRow(600);
                    $table->addCell(null, $cellRowContinue);
                    $table->addCell(1400, $cellCenter)->addText($item['label'], null, $center);
                    $table->addCell(3000, $cellCenter)->addText($item['list'][0]['place_text'], null, $center);
                    $table->addCell(1200, $cellCenter)->addText($hours, null, $center);
                    $table->addCell(2000, $cellCenter)->addText($taskInfo['traffic_text'], null, $center);
                }
            }
        } else {
            // 日期
            $table->addCell(1400, $cellRowMerge);
            // 地点
            $table->addCell(3000, $cellRowMerge);
            // 时长
            $table->addCell(1200, $cellRowMerge);
            // 见证人
            $table->addCell(2000, $cellRowMerge);
        }
        $table->addRow(600);
        $table->addCell(2000, $cellCenter)->addText('接受安全教育', null, $center);
        $table->addCell(1400, $cellCenter)->addText('', null, $center);
        $table->addCell(3000, $cellCenter)->addText('', null, $center);
        $table->addCell(1200, $cellCenter)->addText('', null, $center);
        $table->addCell(2000, $cellCenter)->addText('', null, $center);
        // 评价
        $table->addRow(3000);
        $evaluation = $table->addCell(2000, $cellColMerge);
        $evaluation->addTextBreak();
        $evaluation->addText("评价：", ['cellMargin' => 80], ['indent' => 0.2]);
        $evaluation->addTextBreak();
        $evaluation->addText("好          良好            合格                不合格", null, ['indent' => 1]);
        $evaluation->addTextBreak(3);
        $evaluation->addText("年    月    日", null, ['indent' => 6.5]);
        $evaluation->addText("（印章）", null, ['indent' => 6.8]);
        // 备注
        $section->addTextBreak(1);
        $section->addText('备注：', $noteStyle);
        $section->addText('1、自    年  月  日起3个工作日内到交警部门报到。', $noteListStyle, ['indent' => 0.7]);
        $section->addText('2、公益服务时长不少于5/7/9日，每天不少于2个小时，保证在15个工作日内完成。自    年  月  日  至    年  月  日。', $noteListStyle, ['indent' => 0.7]);
        $section->addText('3、参与社会公益活动后，积极撰写参与公益活动的心得体会或悔过书，并提交检察机关。', $noteListStyle, ['indent' => 0.7]);
        $section->addText('4、报到地点及联系方式：', $noteListStyle, ['indent' => 0.7]);
        $section->addText('交警一大队：交警直属一大队事故处理中队211办公室', $noteListStyle, ['indent' => 0.7]);
        $section->addText('警官 0000--0000000、0000000', $noteListStyle, ['indent' => 3.4]);
        $objWriter = IOFactory::createWriter($phpWord, 'Word2007');
        $report_url = ROOT_PATH . 'public/report/task_' . $task_id . $taskInfo['service_id'] . $taskInfo['traffic_id'] . $taskInfo['place_id'] . $taskInfo['user_id'];
        $objWriter->save($report_url . '.docx');
        // word文档转pdf
        // $tcpPdfPath = VENDOR_PATH.'/tecnickcom/tcpdf';
        // Settings::setPdfRendererPath($tcpPdfPath);
        // Settings::setPdfRendererName('TCPDF');
        // $phpWord = IOFactory::load($report_url.'.docx');
        // $pdfWriter = IOFactory::createWriter($phpWord , 'PDF');
        // $pdfWriter->save($report_url.'.pdf');
        $report_url = str_replace(ROOT_PATH . 'public', '', $report_url) . '.docx';
        $this->where('id', $task_id)->setField('report_url', $report_url);
        // write_log($this->getLastSql());
    }
    return getFileFullUrl($report_url);
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