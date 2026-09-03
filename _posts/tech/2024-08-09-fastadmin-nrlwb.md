---
layout: post
title: Fastadmin导入数据常见问题
category: PHP
keywords: JavaScript Common PHP
tags: JavaScript Common PHP
description: 
---

> Fastadmin导入数据常见问题

1. 导入时间格式(方式一)
```php
public function import()
{
    ...
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
        $row = [];
        $temp = array_combine($fields, $values);
        foreach ($temp as $k => $v) {
            if (isset($fieldArr[$k]) && $k !== '') {
                // 导入时间格式变成浮点数   
                // excel软件中的日期是从 1900-01-01 开始
                // php 从 1970-01-01开始
                // 两者相差 25569 天
                // 时间是格林威治时间
                if ($fieldArr[$k] == 'closing_date') { // 关闭日期
                    $v =  strtotime(gmdate('Y-m-d H:i:s', ($v - 25569) * 24 * 60 * 60));
                }
            }
        }
        if ($row) {
            $insert[] = $row;
        }
    }
    ...
}
```

2. 导入时间格式(方式二)
```php
public function import()
{
    ...
    for ($currentRow = 2; $currentRow <= $allRow; $currentRow++) {
        $values = [];
        for ($currentColumn = 1; $currentColumn <= $maxColumnNumber; $currentColumn++) {
           $val = $currentSheet->getCellByColumnAndRow($currentColumn, $currentRow)->getValue();
            $cell = $currentSheet->getCellByColumnAndRow($currentColumn, $currentRow);
            //批量导入时间格式转化
            if ($cell->getDataType() == DataType::TYPE_NUMERIC) {
                $cellstyleformat = $cell->getStyle($cell->getCoordinate())->getNumberFormat();
                $formatcode = $cellstyleformat->getFormatCode();
                if (preg_match('/^(\[\$[A-Z]*-[0-9A-F]*\])*[hmsdy]/i', $formatcode)) {
                    $dateTime = Date::excelToDateTimeObject($cell->getValue());
                    // 直接格式化输出，它会自动遵循你配置的 'PRC' 时区，出来就是正常的时间
                    $dateStr = $dateTime->format('Y-m-d H:i:s');
                    // 转换为时间戳
                    $val = strtotime($dateStr);
                }
            }
            $values[] = is_null($val) ? '' : $val;
        }
    }
    ...
}
```

参考链接：

fastadmin 导入Excel时间格式错误问题：[https://ask.fastadmin.net/article/21967.html](https://ask.fastadmin.net/article/21967.html)

导入数据的时候excel时间格式为2022/11/17，导入数据表之后时间戳不正确[https://ask.fastadmin.net/question/38766.html](https://ask.fastadmin.net/question/38766.html)

fastadmin 导入Excel有空行问题：[https://ask.fastadmin.net/article/22040.html](https://ask.fastadmin.net/article/22040.html)