---
layout: post
title: FastAdmin对接飞鹅云FP-N20W(云标签机+小票机)
category: PHP
keywords: Common PHP Article
tags: Common PHP Article
description: 
---

#### FastAdmin对接飞鹅云FP-N20W(云标签机+小票机)

1. 配置小票机联网

    短按setting听到滴一声后松开，进入选择wifi设置模式选择，并打印文字，短按setting 1秒进入微信设置wifi，长按5秒进入网页设置wifi方式

2. 切换打印模式

    关机状态下按住PAUSE和FEED按钮然后开机会进入切换模式
    
3. 打印类
    ```
     <?php
     namespace app\common\model;
     use think\Exception;
     use think\Model;
     /**
     * 产品模型
     */
     class Printer extends Model
     {
        // 追加属性
        private $errorArray = [
            '-1' => '请求头错误',
            '-2' => '参数不合法',
            '-3' => '参数签名失败',
            '-4' => '用户未注册',
            '1001' => '打印机编号和用户不匹配',
            '1002' => '打印机未注册',
            '1003' => '打印机不在线',
            '1004' => '添加订单失败',
            '1005' => '未找到订单信息',
            '1006' => '订单日期格式或大小不正确',
            '1007' => '打印内容不能超过12K',
            '1008' => '用户修改打印机记录失败',
            '1009' => '用户添加打印机时，打印机编号或名称不能为空',
            '1010' => '打印机设备编号无效',
            '1011' => '打印机已存在，若当前开放平台无法查询到打印机信息，请联系售后技术支持人员核实',
            '1012' => '添加打印设备失败，请稍后再试或联系售后技术支持人员',
        ];
        /**
        * 添加打印机
        * @param int $device
        * @param string $secret
        * @return bool
        * @throws Exception
        */
        public function add($device = '', $secret = '')
        {
            $FeieModel = new Feie();
            $printerContent = $device . '#' . $secret;
            $response = $FeieModel->printerAddlist($printerContent);
            $response = json_decode($response, true);
            if (!empty($response['msg']) && $response['msg'] == 'ok') {
                if (!empty($response['data']['no'])) { // 添加成功
                    $this->error = $response['data']['no'][0];
                    return false;
                }
            } else {
                $this->error = $response['msg'];
                return false;
            }
        }
        /**
        * 修改打印机
        * @param int $device
        * @return bool
        */
        public function change($device = '')
        {
            $FeieModel = new Feie();
            $response = $FeieModel->printerEdit($device, '飞鹅打印机');
            $response = json_decode($response, true);
            if (!empty($response['msg']) && $response['msg'] == 'ok') {
                return true;
            } else {
                $this->error = $response['msg'];
                return false;
            }
        }
        /**
        * 删除打印机
        * @param int $device
        * @return bool|int
        */
        public function delete($device = 0)
        {
            $FeieModel = new Feie();
            $response = $FeieModel->printerDelList($device);
            $response = json_decode($response, true);
            if (!empty($response['msg']) && $response['msg'] == 'ok') {
                if (!empty($response['data']['no'])) {
                    $this->error = $response['data']['no'];
                    return false;
                } else {
                    return true;
                }
            } else {
                $this->error = $response['msg'];
                return false;
            }
        }
        /**
        * 打印机状态
        * @param int $device
        */
        public function getStatus($device = 0)
        {
            $FeieModel = new Feie();
            $response = $FeieModel->queryPrinterStatus($device);
            $response = json_decode($response, true);
            if (!empty($response['msg']) && $response['msg'] == 'ok') {
                return $response['data'];
            } else {
                $this->error = $response['msg'];
                return false;
            }
        }
        /**
        * 设置打印机设置
        * @param int $device
        */
        public function updatePrinterSetting($device = 0, $autocut = '', $voice = '')
        {
            $FeieModel = new Feie();
            $response = $FeieModel->updatePrinterSetting($device, $autocut, $voice);
            $response = json_decode($response, true);
            if (!empty($response['msg']) && $response['msg'] == 'ok') {
                return true;
            } else {
                $this->error = $response['msg'];
                return false;
            }
        }
        /**
        * 打印订单(小票机)
        * @param array $order
        * @param int $number
        * @return bool
        */
        public function printingOrder($order = [], $number = 1)
        {
            $orderGoodds = [];
            $orderStore = '';
            if (!empty($order)) {
                $orderGoodds = model('addons\groupon\model\OrderItem')
                    ->field('id,goods_title,goods_sku_text,goods_price,goods_num')
                    ->where('order_id', $order['id'])->select();
                $orderStore = model('addons\groupon\model\Store')
                    ->field('id,name')
                    ->where('id', $order['store_id'])->find();
                // write_log($order);
                // 通用打印机模板
                $orderInfo = '<CB>' . $orderStore['name'] . '</CB><BR>';
                $orderInfo .= '下单时间: '.date('Y-m-d H:i:s', $order['paytime']) . '<BR>';
                $orderInfo .= '订单编号:'.$order['order_sn'] . '<BR>';
                $orderInfo .= '名称           单价  数量 金额<BR>';
                $orderInfo .= '--------------------------------<BR><BR>';
                $A = '14';
                $B = '6';
                $C = '3';
                $D = '6';
                $tail = '';
                $nums = 0;
                if (!empty($orderGoodds)) {
                    foreach ($orderGoodds as $k5 => $v5) {
                        $name = $v5['goods_title'] . (!empty($v5['goods_sku_text']) ? $v5['goods_sku_text'] : '');
                        $price = $v5['goods_price'];
                        $num = $v5['goods_num'];
                        $prices = $v5['goods_price'] * $v5['goods_num'];
                        $kw3 = '';
                        $kw1 = '';
                        $kw2 = '';
                        $kw4 = '';
                        $str = $name;
                        $blankNum = $A; //名称控制为14个字节
                        $lan = mb_strlen($str, 'utf-8');
                        $m = 0;
                        $j = 1;
                        $blankNum++;
                        $result = array();
                        if (strlen($price) < $B) {
                            $k1 = $B - strlen($price);
                            for ($q = 0; $q < $k1; $q++) {
                                $kw1 .= ' ';
                            }
                            $price = $price . $kw1;
                        }
                        if (strlen($num) < $C) {
                            $k2 = $C - strlen($num);
                            for ($q = 0; $q < $k2; $q++) {
                                $kw2 .= ' ';
                            }
                            $num = $num . $kw2;
                        }
                        if (strlen($prices) < $D) {
                            $k3 = $D - strlen($prices);
                            for ($q = 0; $q < $k3; $q++) {
                                $kw4 .= ' ';
                            }
                            $prices = $prices . $kw4;
                        }
                        for ($i = 0; $i < $lan; $i++) {
                            $new = mb_substr($str, $m, $j, 'utf-8');
                            $j++;
                            if (mb_strwidth($new, 'utf-8') < $blankNum) {
                                if ($m + $j > $lan) {
                                    $m = $m + $j;
                                    $tail = $new;
                                    $lenght = iconv("UTF-8", "GBK//IGNORE", $new);
                                    $k = $A - strlen($lenght);
                                    for ($q = 0; $q < $k; $q++) {
                                        $kw3 .= ' ';
                                    }
                                    if ($m == $j) {
                                        $tail .= $kw3 . ' ' . $price . ' ' . $num . ' ' . $prices;
                                    } else {
                                        $tail .= $kw3 . '<BR>';
                                    }
                                    break;
                                } else {
                                    $next_new = mb_substr($str, $m, $j, 'utf-8');
                                    if (mb_strwidth($next_new, 'utf-8') < $blankNum) continue;
                                    else {
                                        $m = $i + 1;
                                        $result[] = $new;
                                        $j = 1;
                                    }
                                }
                            }
                        }
                        $head = '';
                        foreach ($result as $key => $value) {
                            if ($key < 1) {
                                $v_lenght = iconv("UTF-8", "GBK//IGNORE", $value);
                                $v_lenght = strlen($v_lenght);
                                if ($v_lenght == 13) $value = $value . " ";
                                $head .= $value . ' ' . $price . ' ' . $num . ' ' . $prices;
                            } else {
                                $head .= $value . '<BR>';
                            }
                        }
                        $orderInfo .= $head . $tail . '<BR>';
                        @$nums += floatval($prices);
                    }
                }
                $orderInfo .= '<BR>------------优惠信息------------<BR><BR>';
                // $orderInfo .= $discountFee;
                $orderInfo .= '<CB>' . ($order['coupon_fee'] > 0 ? ('[优惠券抵用金额' . $order['coupon_fee'] . '元]') : '无') . '</CB><BR>';
                $orderInfo .= '--------------------------------';
                $orderInfo .= '订单总金额：<B>' . $order['total_amount'] . '</B><BR>';
                $orderInfo .= '支付金额：<B>' . $order['total_fee'] . '</B><BR><BR>';
                $orderInfo .= '--------------------------------<BR><BR>';
                $orderInfo .= '<B>' . $order['store']['address'] . '</B><BR><BR>';
                $orderInfo .= '<B>' . $order['consignee'] . '</B><BR><BR>';
                $orderInfo .= '<B>' . substr_cut($order['phone'], 1) . '</B><BR>';
                $orderInfo .= '<B>' . ($order['remark'] ? '备注：' . $order['remark'] : '<BR><BR>');
                $orderInfo .= '*******完*******<BR>';
                // $orderInfo .= '<CUT>';
                // print_r($orderInfo);exit;
                $config = model('addons\groupon\model\Config')->get(['name' => 'services']);
                $config = ($config && $config->value) ? json_decode($config->value, true) : [];
                $response = model('app\common\model\Feie')->printingTemplate($orderInfo, $config['printer']['device']);
                $responseObject = json_decode($response);
                if (!empty($responseObject->ret)) {
                    $this->error = $responseObject->msg;
                    return false;
                }
            }
            return true;
        }
        /**
        * 打印订单(标签机)
        */
        /*
        public function printingOrder($order = [], $number = 1)
        {
            $orderGoodds = [];
            $orderStore = '';
            $printContent = [];
            if (!empty($order)) {
                $orderGoodds = model('addons\groupon\model\OrderItem')
                    ->field('id,goods_title,goods_sku_text,goods_price,pay_price,goods_num')
                    ->where('order_id', $order['id'])->select();
                $orderStore = model('addons\groupon\model\Store')
                    ->field('id,name,images,openweeks')
                    ->where('id', $order['store_id'])
                    ->find();
                // write_log($orderStore);
                $printContent = [];
                // 40mm宽度标签纸一行占用26个字符，50mm宽度标签纸请改成32个字符
                for ($i = 0; $i < count($orderGoodds); $i++) {
                    $goods_sku_text = !empty($orderGoodds[$i]['goods_sku_text']) ? $orderGoodds[$i]['goods_sku_text'] : '无';
                    $content = '<TEXT x="40" y="60" font="12" w="1" h="1" r="0">' . self::LR($order['consignee'], substr_cut($order['phone'], 1), 22) . '</TEXT>';
                    $content .= '<TEXT x="40" y="120" font="12" w="2" h="2" r="0">' . $orderGoodds[$i]["goods_title"] . '</TEXT>';
                    $content .= '<TEXT x="40" y="180" font="12" w="1" h="2" r="0">规格：' . $goods_sku_text . ' * ' . $orderGoodds[$i]['goods_num'] . '</TEXT>';
                    $content .= '<TEXT x="40" y="240" font="12" w="1" h="2" r="0">小计：' . $orderGoodds[$i]["pay_price"] . '</TEXT>';
                    $content .= '<TEXT x="40" y="330" font="12" w="1" h="2" r="0">' . $orderStore['name'] . '</TEXT>';
                    $content .= '<TEXT x="40" y="400" font="12" w="0" h="0" r="0">下单时间：</TEXT>';
                    $content .= '<TEXT x="40" y="440" font="12" w="0" h="0" r="0">' . date('Y-m-d H:i:s', $order['paytime']) . '</TEXT>';
                    $printContent[$i] = [
                        "content" => $content,
                        'times' => 1
                    ];
                }
                $printContents = array_chunk($printContent, 10);
                // write_log($printContents);
                foreach ($printContents as &$item) {
                    $item = json_encode($item);
                }
                $config = model('addons\groupon\model\Config')->get(['name' => 'services']);
                $config = ($config && $config->value) ? json_decode($config->value, true) : [];
                // write_log($config);
                try {
                    foreach ($printContents as $printContentItem) {
                        $response = model('app\common\model\Feie')->printingTemplates($printContentItem, $config['printer']['device']);
                        // write_log($response);
                        $responseObject = json_decode($response);
                        if (!empty($responseObject->ret)) {
                            $this->error = $responseObject->msg;
                            return false;
                        }
                    }
                    return true;
                } catch (\Exception $e) {
                    $this->error = $e->getMessage();
                    return false;
                }
            }
        }
        */
        /**
        * [统计字符串字节数补空格，实现左右排版对齐]
        * @param  [string] $str_left    [左边字符串]
        * @param  [string] $str_right   [右边字符串]
        * @param  [int]    $length      [输入当前纸张规格一行所支持的最大字母数量]
        *                               58mm的机器,一行打印16个汉字,32个字母;76mm的机器,一行打印22个汉字,33个字母,80mm的机器,一行打印24个汉字,48个字母
        *                               标签机宽度50mm，一行32个字母，宽度40mm，一行26个字母
        * @return [string]              [返回处理结果字符串]
        */
        public function LR($str_left, $str_right, $length)
        {
            if (empty($str_left) || empty($str_right) || empty($length)) {
                return '请输入正确的参数';
            }
            $kw = '';
            $str_left_lenght = strlen(iconv("UTF-8", "GBK//IGNORE", $str_left));
            $str_right_lenght = strlen(iconv("UTF-8", "GBK//IGNORE", $str_right));
            $k = $length - ($str_left_lenght + $str_right_lenght);
            for ($q = 0; $q < $k; $q++) {
                $kw .= ' ';
            }
            return $str_left . $kw . $str_right;
        }
     }

    ```

4. 飞鹅云SDK
    ```
    <?php
    namespace app\common\model;
    use think\Model;
    use app\common\library\HttpClient;
    /**
    * 飞鹅模型
    */
    class Feie extends Model
    {
        protected static $User = ''; //*必填*：飞鹅云后台注册账号
        protected static $UKey = ''; //*必填*: 飞鹅云后台注册账号后生成的UKey 【备注：这不是填打印机的KEY】
        protected static $IP = 'api.feieyun.cn'; //接口IP或域名
        protected static $Port = '80'; //接口IP端口
        protected static $Path = '/Api/Open/'; //接口路径
        /**
        * 打印订单(小票机)
        * @param $printerContent
        * @param $printId
        */
        public function printingTemplate($printerContent = '', $device = '')
        {
            $response = self::printMsg($device, $printerContent, 1);
            return $response;
        }
        /**
        * 打印订单(标签机)
        * @param $printerContent
        * @param $printId
        */
        public function printingTemplates($printerContent = '', $device = '')
        {
            $response = self::printLabelMsg($device, $printerContent, 1);
            return $response;
        }
        /**
        * [批量添加打印机接口 Open_printerAddlist]
        * @param  [string] $printerContent [打印机的sn#key]
        * @return [string]                 [接口返回值]
        */
        function printerAddlist($printerContent)
        {
            $time = time(); //请求时间
            $msgInfo = array(
                'user' => self::$User,
                'stime' => $time,
                'sig' => self::signature($time),
                'apiname' => 'Open_printerAddlist',
                'printerContent' => $printerContent
            );
            $client = new HttpClient(self::$IP, self::$Port);
            if (!$client->post(self::$Path, $msgInfo)) {
                return false;
            } else {
                return $client->getContent();
            }
        }
        /**
        * [打印订单接口 Open_printMsg]
        * @param  [string] $sn      [打印机编号sn]
        * @param  [string] $content [打印内容]
        * @param  [string] $times   [打印联数]
        * @return [string]          [接口返回值]
        */
        static function printMsg($sn, $content, $times)
        {
            $time = time(); //请求时间
            $msgInfo = array(
                'user' => self::$User,
                'stime' => $time,
                'sig' => self::signature($time),
                'apiname' => 'Open_printMsg',
                'sn' => $sn,
                'content' => $content,
                'times' => $times //打印次数
            );
            $client = new HttpClient(self::$IP, self::$Port);
            if (!$client->post(self::$Path, $msgInfo)) {
                return false;
            } else {
                //服务器返回的JSON字符串，建议要当做日志记录起来
                return $client->getContent();
            }
        }
        /**
        * [标签机打印订单接口 Open_printLabelMsg]
        * @param  [string] $sn      [打印机编号sn]
        * @param  [string] $content [打印内容]
        * @param  [string] $times   [打印联数]
        * @return [string]          [接口返回值]
        */
        function printLabelMsg($sn, $content, $times)
        {
            $time = time(); //请求时间
            $msgInfo = array(
                'user' => self::$User,
                'stime' => $time,
                'sig' => self::signature($time),
                'apiname' => 'Open_printLabelMsg',
                'sn' => $sn,
                'contents' => $content,
                'times' => $times //打印次数
            );
            $client = new HttpClient(self::$IP, self::$Port);
            if (!$client->post(self::$Path, $msgInfo)) {
                return false;
            } else {
                //服务器返回的JSON字符串，建议要当做日志记录起来
                return $client->getContent();
            }
        }
        /**
        * [批量删除打印机 Open_printerDelList]
        * @param  [string] $snlist [打印机编号，多台打印机请用减号“-”连接起来]
        * @return [string]         [接口返回值]
        */
        function printerDelList($snlist)
        {
            $time = time(); //请求时间
            $msgInfo = array(
                'user' => self::$User,
                'stime' => $time,
                'sig' => self::signature($time),
                'apiname' => 'Open_printerDelList',
                'snlist' => $snlist
            );
            $client = new HttpClient(self::$IP, self::$Port);
            if (!$client->post(self::$Path, $msgInfo)) {
                return false;
            } else {
                return $client->getContent();
            }
        }
        /**
        * [修改打印机信息接口 Open_printerEdit]
        * @param  [string] $sn       [打印机编号]
        * @param  [string] $name     [打印机备注名称]
        * @param  [string] $phonenum [打印机流量卡号码,可以不传参,但是不能为空字符串]
        * @return [string]           [接口返回值]
        */
        function printerEdit($sn, $name, $phonenum = '')
        {
            $time = time(); //请求时间
            $msgInfo = array(
                'user' => self::$User,
                'stime' => $time,
                'sig' => self::signature($time),
                'apiname' => 'Open_printerEdit',
                'sn' => $sn,
                'name' => $name,
            );
            if (!empty($phonenum)) {
                $msgInfo['phonenum'] = $phonenum;
            }
            $client = new HttpClient(self::$IP, self::$Port);
            if (!$client->post(self::$Path, $msgInfo)) {
                return false;
            } else {
                return $client->getContent();
            }
        }
        /**
        * [清空待打印订单接口 Open_delPrinterSqs]
        * @param  [string] $sn [打印机编号]
        * @return [string]     [接口返回值]
        */
        function delPrinterSqs($sn)
        {
            $time = time(); //请求时间
            $msgInfo = array(
                'user' => self::$User,
                'stime' => $time,
                'sig' => self::signature($time),
                'apiname' => 'Open_delPrinterSqs',
                'sn' => $sn
            );
            $client = new HttpClient(self::$IP, self::$Port);
            if (!$client->post(self::$Path, $msgInfo)) {
                return false;
            } else {
                return $client->getContent();
            }
        }
        /**
        *飞鹅云接口文档没有,飞鹅平台有
        * [修改打印机信息接口 Open_printerEdit]
        * @param  [string] $sn       [打印机编号]
        * @param  [string] $name     [打印机备注名称]
        * @param  [string] $phonenum [打印机流量卡号码,可以不传参,但是不能为空字符串]
        * @return [string]           [接口返回值]
        */
        function updatePrinterSetting($sn, $autocut = '', $voice = '')
        { //
            $time = time(); //请求时间
            $msgInfo = array(
                'user' => self::$User,
                'stime' => $time,
                'sig' => self::signature($time),
                'apiname' => 'Printer_setPrinterConfig',
                'sn' => $sn,
            );
            if (!empty($autocut)) {
                $msgInfo['printautocut'] = $autocut;
            }
            if (!empty($voice)) {
                $msgInfo['voice'] = $voice;
            }
            $client = new HttpClient(self::$IP, self::$Port);
            if (!$client->post(self::$Path, $msgInfo)) {
                return false;
            } else {
                return $client->getContent();
            }
        }
        /**
        * [查询订单是否打印成功接口 Open_queryOrderState]
        * @param  [string] $orderid [调用打印机接口成功后,服务器返回的JSON中的编号 例如：123456789_20190919163739_95385649]
        * @return [string]          [接口返回值]
        */
        function queryOrderState($orderid)
        {
            $time = time(); //请求时间
            $msgInfo = array(
                'user' => self::$User,
                'stime' => $time,
                'sig' => self::signature($time),
                'apiname' => 'Open_queryOrderState',
                'orderid' => $orderid
            );
            $client = new HttpClient(self::$IP, self::$Port);
            if (!$client->post(self::$Path, $msgInfo)) {
                return false;
            } else {
                return $client->getContent();
            }
        }
        /**
        * [查询指定打印机某天的订单统计数接口 Open_queryOrderInfoByDate]
        * @param  [string] $sn   [打印机的编号]
        * @param  [string] $date [查询日期，格式YY-MM-DD，如：2019-09-20]
        * @return [string]       [接口返回值]
        */
        function queryOrderInfoByDate($sn, $date)
        {
            $time = time(); //请求时间
            $msgInfo = array(
                'user' => self::$User,
                'stime' => $time,
                'sig' => self::signature($time),
                'apiname' => 'Open_queryOrderInfoByDate',
                'sn' => $sn,
                'date' => $date
            );
            $client = new HttpClient(self::$IP, self::$Port);
            if (!$client->post(self::$Path, $msgInfo)) {
                return false;
            } else {
                return $client->getContent();
            }
        }
        /**
        * [获取某台打印机状态接口 Open_queryPrinterStatus]
        * @param  [string] $sn [打印机编号]
        * @return [string]     [接口返回值]
        */
        function queryPrinterStatus($sn)
        {
            $time = time(); //请求时间
            $msgInfo = array(
                'user' => self::$User,
                'stime' => $time,
                'sig' => self::signature($time),
                'apiname' => 'Open_queryPrinterStatus',
                'sn' => $sn
            );
            $client = new HttpClient(self::$IP, self::$Port);
            if (!$client->post(self::$Path, $msgInfo)) {
                return false;
            } else {
                return $client->getContent();
            }
        }
        /**
        * [self::signature 生成签名]
        * @param  [string] $time [当前UNIX时间戳，10位，精确到秒]
        * @return [string]       [接口返回值]
        */
        static function signature($time)
        {
            return sha1(self::$User . self::$UKey . $time); //公共参数，请求公钥
        }
    }
    ```