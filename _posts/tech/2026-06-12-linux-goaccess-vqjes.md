---
layout: post
title: 利用Linux GoAccess 生成访问日志
category: Linux
keywords: Common Linux
tags: Common Linux
description: 
---

#### 利用Linux GoAccess 生成访问日志

1. 安装GoAccess
```
pip install GoAccess
```
2. 生成中文Html页面
```
LANG="zh_CN.UTF-8" goaccess /www/wwwlogs/site.log -o /file/analysis/site.html --log-format=COMBINED
```
3. 优化页面文字
```
# 1. 生成 HTML 报告
LC_ALL="zh_CN.UTF-8" LC_TIME="zh_CN.UTF-8" LANG="zh_CN.UTF-8" goaccess /www/wwwlogs/site.log -o /file/analysis/site.html --log-format='%h %^[%d:%t %^] "%r" %s %b "%R" "%u"' --date-format='%d/%b/%Y' --time-format='%H:%M:%S'
# 2. 基础单词强制替换
sed -i 's/Max:/最大:/g; s/Min:/最小:/g; s/Max /最大 /g; s/Min /最小 /g;' /file/analysis/site.html
sed -i 's/Tx. Amount/传输总量/g' /file/analysis/site.html
# 3. 终极一剑：不论是在普通文本还是在底层 JSON 数据中，凡是符合 12 个月份格式的，全部强制重组为 年-月-日
# 针对英文月份 (如 12/Jun/2026 -> 2026-06-12)
sed -i -E 's/([0-9]{2})\/Jan\/([0-9]{4})/\2-01-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/Feb\/([0-9]{4})/\2-02-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/Mar\/([0-9]{4})/\2-03-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/Apr\/([0-9]{4})/\2-04-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/May\/([0-9]{4})/\2-05-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/Jun\/([0-9]{4})/\2-06-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/Jul\/([0-9]{4})/\2-07-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/Aug\/([0-9]{4})/\2-08-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/Sep\/([0-9]{4})/\2-09-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/Oct\/([0-9]{4})/\2-10-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/Nov\/([0-9]{4})/\2-11-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/Dec\/([0-9]{4})/\2-12-\1/g' /file/analysis/site.html
# 针对中英混杂月份 (如 12/6月/2026 -> 2026-06-12)
sed -i -E 's/([0-9]{2})\/1月\/([0-9]{4})/\2-01-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/2月\/([0-9]{4})/\2-02-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/3月\/([0-9]{4})/\2-03-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/4月\/([0-9]{4})/\2-04-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/5月\/([0-9]{4})/\2-05-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/6月\/([0-9]{4})/\2-06-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/7月\/([0-9]{4})/\2-07-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/8月\/([0-9]{4})/\2-08-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/9月\/([0-9]{4})/\2-09-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/10月\/([0-9]{4})/\2-10-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/11月\/([0-9]{4})/\2-11-\1/g' /file/analysis/site.html
sed -i -E 's/([0-9]{2})\/12月\/([0-9]{4})/\2-12-\1/g' /file/analysis/site.html
```

备注： 经测试1.3版本除日期外其他都汉化了