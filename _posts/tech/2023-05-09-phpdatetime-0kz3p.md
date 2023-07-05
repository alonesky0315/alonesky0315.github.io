---
layout: post
title: PHP的DateTime类详解
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

### PHP的DateTime类详解

类的官网地址：[https:// www.php.net/manual/zh/class.datetime.php](https:// www.php.net/manual/zh/class.datetime.php)

PHP5.5起，DateTime类实现DateTimeInterface接口。

DateTime包括12个常量和除DateTimeInterface接口中定义的方法外的12个方法：

__construct：接受两个参数，一个是可选的日期时间字符串，如果字符串为now，表示获取当前时间。第二个参数是可选的DateTimeZone对象，用于指定获取哪个时区的时间，如果不传入此参数则使用默认为当前时间。当时间字符串为now时，如果同时传入了DateTimeZone对象，则会获取指定时区的当前时间。如果日期时间字符串为Unix时间戳或者已经包含了时区信息，则会忽略传入的DateTimeZone对象以及当前时区，返回一个新的DateTime对象，如果失败返回false。PHP5.3起，如果日期时间字符串格式错误会抛出异常，在此之前则是产生一个错误。PHP7.1起，微秒部分不再是0000而是返回真实的微秒数据。PHP5.2起，可用UNIX时间戳做为构造函数的参数生成新的DateTime对象。

add：增加一定的时间（天、月、年、小时、分钟以及秒）。接受一个DateInterval对象，将DateInterval对象的时间加到DateTime对象上。返回被修改的DateTime对象，若失败则返回false。

createFromFormat：静态方法，根据指定的格式解析日期时间字符串。接受三个参数，第一个为格式字符串，第二个参数为日期时间字符串，第三个参数为可选的DateTimeZone对象，用于指定获取哪个时区的时间，如果不传入此参数，且日期时间字符串中不包含时区信息则使用默认为当前时间。如果日期时间字符串为Unix时间戳或者已经包含了时区信息，则会忽略传入的DateTimeZone对象以及当前时区。如果格式字符串中存在不可识别的字符，会导致解析失败，并在返回的结果中附加一个错误信息，可以通过getLastErrors方法查看解析是否存在错误。返回一个DateTime对象，如果失败返回false。

getLastErrors：静态方法，获取解析日期时间字符串的过程中发生的警告和错误信息。返回一个数组，包含警告数量，警告信息，错误数量，错误信息，其中警告信息与错误信息是数组，数组的键为产生警告或错误的字符位置，值为具体警告或错误信息。

modify：修改日期时间对象的值，根据传入的日期时间字符串，修改DateTime对象的值。

__set_state：魔术方法。

setDate：设置DateTime对象的日期，接受三个参数，分别为年、月、日。返回被修改的DateTime对象，失败则返回false。

setISODate：以ISO8601的格式设置日期，使用周和日做偏移量而不是月和日。接受三个参数，第一个是年，第二个是周，第三个是可选的每周的第几天，默认为1。返回被修改的DateTime对象，失败则返回false。

setTime：设置DateTime对象的时间，接受四个参数，分别为时、分、秒、微秒（PHP7.1起），其中秒和微秒为可选的，默认为0。返回被修改的DateTime对象，失败则返回false。

setTimestamp：以Unix时间戳的方式设置DateTime对象的日期和时间。返回被修改的DateTime对象，失败则返回false。

setTimezone：设置DateTime对象的时区。接受一个DateTimeZone对象，表示要设置的时区。返回被修改的DateTime对象，失败则返回false。

sub：与add相反，减去一定的时间（天、月、年、小时、分钟以及秒）。接受一个DateInterval对象，将DateInterval对象的时间加到DateTime对象上。返回被修改的DateTime对象，若失败则返回false。

方法示例：

```
// 实例化一个类，输出当前的时间

$date = new DateTime();
echo $date->format('Y-m-d H:i:s');echo '<br>';

// 输出时间戳的时间格式 类似于time()函数

echo $date->format('U');echo '<br>';
echo $date->getTimestamp();echo '<br>';

// 带有参数的实例化形式，参数一为时间字符串，参数二为时区

$date = new DateTime('now',new DateTimeZone('Asia/Shanghai'));
echo $date->format('Y-m-d H:i:s');echo '<br>';

// 获取特定的时间示例

$date = new DateTime('2021-05-04');
echo $date->format('Y-m-d H:i:s');echo '<br>';// 2021-05-04 00:00:00

$date2 = new DateTime('tomorrow');
echo $date2->format('Y-m-d H:i:s');echo '<br>';// 2022-10-19 00:00:00

$date2 = new DateTime('+2 days');
echo $date2->format('Y-m-d H:i:s');echo '<br>';// 2022-10-20 22:51:24

// add方法

$date = new DateTime();
$date->add(new DateInterval('P1D'));
echo $date->format('Y-m-d H:i:s');echo '<br>';

// modify方法(修改时间的方法)

$date->modify('+1 day');
echo $date->format('Y-m-d H:i:s');echo '<br>';// 2022-10-19 23:02:34

// setDate方法(设置年月日的方法)

$date->setDate(1989,11,10);
echo $date->format('Y-m-d H:i:s');echo '<br>';// 1989-11-10 22:57:10

// 设置十分秒的方法

$date->setTime(11,10,10);
echo $date->format('Y-m-d H:i:s');echo '<br>';// 1989-11-10 11:10:10

// 设置时区的方法

$date->setTimezone(new DateTimeZone('Asia/Shanghai'));

// 设置int时间戳类型的方法

$date->setTimestamp(1408950651);

// 两个日期的比较

$date1 = new DateTime();
$date2 = new DateTime('2025-09-15');
if($date1 < $date2) {
    echo $date2->format('Y-m-d H:i:s') . ' 是大';echo '<br>';
}else{
    echo $date1->format('Y-m-d H:i:s') . ' 是大';echo '<br>';
}

// 时间间隔

$diff = $date1->diff($date2);
echo $diff->format("相差 %Y 年 %m 个月 and %d 天");echo '<br>';

/**
 * 时区转换方法
 * $src 源时间
 * $from_tz 源时区
 * $to_tz 要转换到的时区
 * $fm 要转换到的时区的时间格式
 */

function toTimeZone($src = '2022-10-18 23:30:00', $from_tz = 'Asia/Shanghai', $to_tz = 'America/Denver', $fm = 'Y-m-d H:i:s')
{
    $datetime = new DateTime($src, new DateTimeZone($from_tz));
    $datetime->setTimezone(new DateTimeZone($to_tz));
    return $datetime->format($fm);
}
echo toTimeZone();echo '<br>';// 2022-10-18 09:30:00

```

原文链接：[https://blog.csdn.net/alashan007/article/details/127398826](https://blog.csdn.net/alashan007/article/details/127398826)