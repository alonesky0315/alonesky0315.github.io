---
layout: post
title: Laravel 打印完整sql语句
category: PHP
keywords: Common PHP
tags: Common PHP
description: 
---

#### laravel 打印完整sql语句

1. 用DB自带的getQueryLog方法直接打印(语句与参数分开)
```
use Illuminate\Support\Facades\DB;
DB::connection()->enableQueryLog();  // 开启QueryLog
App\User::find(1);
dd(DB::getQueryLog());exit;
```

	得到的结果语句与参数是分开的，非常不方便验证

	```
	array:1 [▼
		0 => array:3 [▼
			"query" => "select * from `users` where `id` = ? order by `created_at` desc"
			"bindings" => array:1 [▼
				0 => "1"
			]
			"time" => 0.44
		]
	]
	```

2. 修改app\Providers\AppServiceProvider.php文件(语句与参数在一起)
```
DB::listen(
    function ($query) {
        foreach ($query->bindings as $i => $binding) {
            if ($binding instanceof \DateTime) {
                $query->bindings[$i] = $binding->format('\'Y-m-d H:i:s\'');
            } else {
                if (is_string($binding)) {
                    $query->bindings[$i] = "'" . $binding . "'";
                }
            }
        }
        // Insert bindings into sql
        $sql = str_replace(array('%', '?'), array('%%', '%s'), $query->sql);
        $sql = vsprintf($sql, $query->bindings);
        // Save the sql to file
        $logFile = fopen(storage_path('logs' . DIRECTORY_SEPARATOR . date('Y-m-d') . '_query.log'), 'a+');
        fwrite($logFile, date('Y-m-d H:i:s') . ': ' . $sql . PHP_EOL);
        fclose($logFile);
    }
);
```

	日志在storage/log/xxx_query.log

3. 将(2)放在需要打印SQL的语句前
```
DB::listen(
    function ($query) {
        foreach ($query->bindings as $i => $binding) {
            if ($binding instanceof \DateTime) {
                $query->bindings[$i] = $binding->format('\'Y-m-d H:i:s\'');
            } else {
                if (is_string($binding)) {
                    $query->bindings[$i] = "'" . $binding . "'";
                }
            }
        }
        // Insert bindings into sql
        $sql = str_replace(array('%', '?'), array('%%', '%s'), $query->sql);
        $sql = vsprintf($sql, $query->bindings);
        dd($sql);
    }
);
```

4. 故意打错误SQL语句查看报错信息

参考链接：

[https://blog.csdn.net/buer2202/article/details/75364465](https://blog.csdn.net/buer2202/article/details/75364465)

[http://www.manongjc.com/detail/25-kunniogktwerccl.html](http://www.manongjc.com/detail/25-kunniogktwerccl.html)