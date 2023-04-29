---
layout: post
title: ThinkPHP生成Html文档并转为PDF(TCPDF)--样式未调整
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

安装TCPDF

```
composer require tecnickcom/tcpdf
```

```
use TCPDF;
/**
 * 生成html文档
 * @param int $task_id 任务Id
 * @return string
 * @throws \think\db\exception\DataNotFoundException
 * @throws \think\db\exception\ModelNotFoundException
 * @throws \think\exception\DbException
 */
public function generateReports2($task_id = 1)
{
    $taskInfo = $this->getTaskInfo($task_id);
    if (!empty($taskInfo['report_url']) && 0) {
        $report_url = $taskInfo['report_url'];
    } else {
        $report_url = ROOT_PATH . 'public/report/task_' . $task_id . $taskInfo['service_id'] . $taskInfo['traffic_id'] . $taskInfo['place_id'] . $taskInfo['user_id'];
        $content = file_get_contents($report_url . '.html');
        $this->htmlToPdf($content, '参与社会公益服务工作记录表', '参与社会公益服务工作记录表');
    }
    return getFileFullUrl($report_url . '.html');
}

//生成pdf
public function htmlToPdf($html = '', $title = "", $fileName = "")
{
    $pdf = new TCPDF(PDF_PAGE_ORIENTATION, PDF_UNIT, PDF_PAGE_FORMAT, true, 'UTF-8', false);
    // 设置打印模式
    //设置文件信息，头部的信息设置
    $pdf->SetCreator(PDF_CREATOR);
    $pdf->SetAuthor("ZhiShun");
    $pdf->SetTitle($title);
    $pdf->SetSubject('ZhiShun');
    $pdf->SetKeywords('参与社会公益服务工作记录表, PDF, ZhiShun');//设置关键字
    $pdf->setPrintHeader(false);
    $pdf->setPrintFooter(false);
    // 设置默认等宽字体
    $pdf->SetDefaultMonospacedFont(PDF_FONT_MONOSPACED);
    // 设置行高
    $pdf->setCellHeightRatio(1);
    // 设置左、上、右的间距
    $pdf->SetMargins('10', '10', '10');
    // 设置是否自动分页 距离底部多少距离时分页
    $pdf->SetAutoPageBreak(TRUE, '15');
    // 设置图像比例因子
    $pdf->setImageScale(PDF_IMAGE_SCALE_RATIO);
    $pdf->setFontSubsetting(true);
    $pdf->AddPage("A4", "Landscape", true, true);
    // 设置字体
    $pdf->SetFont('stsongstdlight', '', 14, '', true);
    $pdf->writeHTML($html);//HTML生成PDF
    //$pdf->writeHTMLCell(0, 0, '', '', $html, 0, 1, 0, true, '', true);
    $showType = 'F';//PDF输出的方式。I，在浏览器中打开；D，以文件形式下载；F，保存到服务器中；S，以字符串形式输出；E：以邮件的附件输出。
    ob_end_clean();
    $path = ROOT_PATH . 'public/report/';
    //判断保存目录是否存在，不存在则进行创建
    if (!is_dir($path)) {
        mkdir($path, '0777', true);
    }
    $pdf->Output($path . $fileName . ".pdf", $showType);
}
```