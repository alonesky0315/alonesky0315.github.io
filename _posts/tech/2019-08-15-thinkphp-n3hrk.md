---
layout: post
title: ThinkPHP3.2对接酷家乐接口
category: PHP
keywords: 
tags: PHP
description: 
---

### ThinkPHP3.2对接酷家乐接口
UserController.class.php        
``` 
namespace User\Controller;
class UserController extends Controller {
    public static $appkey=appkey;
    public static $appsecret=appsecret;
    //注册用户
    public function reg(){
    	$appuid='alonesky0315';
    	//接口鉴权
        $url='https://openapi.kujiale.com/v2/login';
        $postfields=array(
            'name'=>$appuid
        );
        $apidata=$this->getapi($appuid,$url,'post', $postfields);
    }
    //设计列表页
    public function diy_design() {
        $page=I('page',0);
        $appuid='alonesky0315';
        $url='https://openapi.kujiale.com/v2/design/list';
        $postfields=array(
            'start'=>0,
            'num'=>50,
            'appuid'=>$appuid
        );
        $apidata=$this->getapi($appuid,$url,'get', $postfields);
        //print_r($apidata);exit;
        $designlist=$apidata['result'];
        $this->assign("designlist",$designlist);
        $this->display();
    }
    //设计详情页
    public function design_view() {
        $designid=I('designid',0);
        $url='https://openapi.kujiale.com/v2/design/'.$designid.'/basic';
        $postfields=array();
        $apidata=$this->getapi('',$url,'get', $postfields);
        $designinfo=$apidata;
        $this->assign("designinfo",$designinfo);
        $this->display();
    }
    //创建户型
    public function design_create() {
        //3FO4JPFI0DTV
        $appuid='alonesky0315';
        $planId=I('planId','3FO4HTDPPS1A');
        //复制户型接口
        $url='https://openapi.kujiale.com/v2/floorplan/'.$planId.'/copy';
        $postfields=array(
            'appuid'=>$appuid
        );
        $apidata=$this->getapi($appuid,$url,'post', $postfields);
        if(!empty($apidata)){
            $getplanId=$apidata['planId'];
            //登录接口鉴权
            $url='https://openapi.kujiale.com/v2/login';
            $postfields=array(
                'name'=>$appuid
            );
            $access_token=$this->getapi($appuid,$url,'post', $postfields);
            $iframe_src='https://www.kujiale.com/v/auth?accesstoken='.$access_token.'&dest=1&designid='.$getplanId.'';
            $this->assign("iframe_src",$iframe_src);
        }
        $this->assign("title",'创建户型');
        $this->display();
    }
    //户型修改
    public function plan_edit() {
        $appuid='alonesky0315';
        $planId=I('planId','3FO4HTDPPS1A');
        $url='https://openapi.kujiale.com/v2/login';
        $postfields=array(
            'name'=>$appuid
        );
        $access_token=$this->getapi($appuid,$url,'post', $postfields);
        $iframe_src='https://www.kujiale.com/v/auth?accesstoken='.$access_token.'&dest=2&planId='.$planId.'';
        $this->assign("iframe_src",$iframe_src);
        $this->assign("title",'户型修改');
        $this->display('design_create');
    }
    //设计修改
    public function design_edit() {
        $appuid='alonesky0315';
        $designid=I('designid','3FO4HTDPPS1A');
        $url='https://openapi.kujiale.com/v2/login';
        $postfields=array(
            'name'=>$appuid
        );
        $access_token=$this->getapi($appuid,$url,'post', $postfields);
        $iframe_src='https://www.kujiale.com/v/auth?accesstoken='.$access_token.'&dest=6&designid='.$designid.'';
        $this->assign("iframe_src",$iframe_src);
        $this->assign("title",'设计修改');
        $this->display('design_create');
    }
    //设计删除
    public function design_delete() {
        $sign=$this->getsign($appuid);
        $timestamp=time()*1000;
        $designid=I('designid','3FO4HTDPPS1A');
        $url='https://openapi.kujiale.com/v2/design/deletion?appkey='.self::$appkey.'&timestamp='.$timestamp.'&sign='.$sign.'&plan_id='.$designid.'';
        $at_data=$this->curl($url,'post',$postfields='');
        $at_data_de=json_decode($at_data,true);
        if(empty($at_data_de['c'])){
            $this->success('删除成功！',U('User/diy_design'));
        }else{
            $this->errot('删除失败！',U('User/diy_design'));
        }
    }
    //获取签名
    public function getsign($appuid=''){
        $timestamp=time()*1000;
        if(!empty($appuid)){
            $sign=md5(self::$appsecret.self::$appkey.$appuid.$timestamp);
        }else{
            $sign=md5(self::$appsecret.self::$appkey.$timestamp);
        }
        return $sign;
    }
    //获取拼接接口信息
    public function getapi($appuid='',$url='',$method='get', $postfields=array()){
        $sign=$this->getsign($appuid);
        $timestamp=time()*1000;
        if($method=='get'){
            if(!empty($appuid)){
                $url=$url.'?appkey='.self::$appkey.'&timestamp='.$timestamp.'&appuid='.$appuid.'&sign='.$sign.'';
            }else{
                $url=$url.'?appkey='.self::$appkey.'&timestamp='.$timestamp.'&sign='.$sign.'';
            }
            $http_build_query=is_array($postfields) ? http_build_query($postfields) : $postfields;
            $url =$url.'&'.$http_build_query;
        }else{
            if(!empty($appuid)){
                $url=$url.'?appkey='.self::$appkey.'&timestamp='.$timestamp.'&appuid='.$appuid.'&sign='.$sign.'';
            }else{
                $url=$url.'?appkey='.self::$appkey.'&timestamp='.$timestamp.'&sign='.$sign.'';
            }
            $postfields=json_encode($postfields);
        }
        $at_data=$this->curl($url,$method,$postfields);
        $at_data_de=json_decode($at_data,true);
        if(empty($at_data_de['c'])){
            $design=$at_data_de['d'];
        }else{
            $design=$at_data_de['m'];
        }
        return $design;
    }

    /**
     * CURL请求
     * @param $url 请求url地址
     * @param $method 请求方法 get post
     * @param null $postfields post数据数组
     * @param array $headers 请求header信息
     * @param bool|false $debug  调试开启 默认false
     * @return mixed
     */
    public function curl($url, $method, $postfields = null, $headers = array("Content-type:application/json;charset='utf-8'"), $debug = false) {
        $method = strtoupper($method);
        $ci = curl_init();
        /* Curl settings */
        curl_setopt($ci, CURLOPT_HTTP_VERSION, CURL_HTTP_VERSION_1_0);
        curl_setopt($ci, CURLOPT_USERAGENT, "Mozilla/5.0 (Windows NT 6.2; WOW64; rv:34.0) Gecko/20100101 Firefox/34.0");
        curl_setopt($ci, CURLOPT_CONNECTTIMEOUT, 60); /* 在发起连接前等待的时间，如果设置为0，则无限等待 */
        curl_setopt($ci, CURLOPT_TIMEOUT, 7); /* 设置cURL允许执行的最长秒数 */
        curl_setopt($ci, CURLOPT_RETURNTRANSFER, true);
        switch ($method) {
            case "POST":
                curl_setopt($ci, CURLOPT_POST, true);
                if (!empty($postfields)) {
                    $tmpdatastr = is_array($postfields) ? http_build_query($postfields) : $postfields;
                    curl_setopt($ci, CURLOPT_POSTFIELDS, $tmpdatastr);
                }
                break;
            default:
                curl_setopt($ci, CURLOPT_CUSTOMREQUEST, $method); /* //设置请求方式 */
                break;
        }
        $ssl = preg_match('/^https:\/\//i',$url) ? TRUE : FALSE;
        curl_setopt($ci, CURLOPT_URL, $url);
        if($ssl){
            curl_setopt($ci, CURLOPT_SSL_VERIFYPEER, FALSE); // https请求 不验证证书和hosts
            curl_setopt($ci, CURLOPT_SSL_VERIFYHOST, FALSE); // 不从证书中检查SSL加密算法是否存在
        }
        //curl_setopt($ci, CURLOPT_HEADER, true); /*启用时会将头文件的信息作为数据流输出*/
        curl_setopt($ci, CURLOPT_FOLLOWLOCATION, 1);
        curl_setopt($ci, CURLOPT_MAXREDIRS, 2);/*指定最多的HTTP重定向的数量，这个选项是和CURLOPT_FOLLOWLOCATION一起使用的*/
        curl_setopt($ci, CURLOPT_HTTPHEADER, $headers);
        curl_setopt($ci, CURLINFO_HEADER_OUT, true);
        /*curl_setopt($ci, CURLOPT_COOKIE, $Cookiestr); * *COOKIE带过去** */
        $response = curl_exec($ci);
        $requestinfo = curl_getinfo($ci);
        $http_code = curl_getinfo($ci, CURLINFO_HTTP_CODE);
        if ($debug) {
            echo "=====post data======\r\n";
            var_dump($postfields);
            echo "=====info===== \r\n";
            print_r($requestinfo);
            echo "=====response=====\r\n";
            print_r($response);
        }
        curl_close($ci);
        return $response;
        //return array($http_code, $response,$requestinfo);
    }
    
}
```
IndexController.class.php
```
//酷家乐区域
//http://qhstatic.oss.aliyuncs.com/openapi/cities.json?kpm=qkWL.005853d39357deef.729e22f.1564992506606
namespace Index\Controller;
class IndexController extends Controller {
    public static $appkey=appkey;
    public static $appsecret=appsecret;
    //搜索酷家乐户型
    public function floorplan(){
        $cityid=I('cityid','235');
        $cityname=I('cityname','临沂');
        $q=I('q','杏坛');
        $page=I('page','0');
        $start=($page-1)*9;
        $num=9;
        $userCont=new UserController();
        $url='https://openapi.kujiale.com/v2/floorplan/standard';
        $postfields=array(
            'start'=>$start,
            'num'=>$num,
            'city_id'=>$cityid,
            'q'=>$q,
        );
        $apidata=$userCont->getapi('',$url,'get', $postfields);
        if(!empty($apidata)){
            $str='';
            foreach ($apidata['result'] as $item) {
                $url=U('User/design_create',array('planId'=>$item[planId],'srcarea'=>$item[srcArea],'area'=>$item[area],'commname'=>$item[commName],'plancity'=>$item[city],'specname'=>$item[specName],'smallpics'=>$item[planPic],'name'=>$item[name],'cityid'=>$item[srcArea],'cityname'=>$cityname));
                $str.='<li>';
                $str.='<a class="chanpin01 createDesign" href="'.$url.'" data-planid="'.$item['planId'].'" data-srcarea="'.$item['srcArea'].'" data-area="'.$item['area'].'" data-commname="'.$item['commName'].'" data-plancity="'.$item['city'].'" data-specname="'.$item['specName'].'" data-smallpics="'.$item['planPic'].'" data-name="'.$item['name'].'"  data-cityid="'.$item['srcArea'].'" data-cityname="'.$cityname.'" ></a>';
                $str.='<img src="'.$item['planPic'].'" width="288" height="288" />';
                $str.='<div style="clear:both"></div>';
                $str.='<p>'.$item['name'].'</p>';
                $str.='<p>'.$item['commName'].'</p>';
                $str.='</li>';
            }
            $data=array(
                'status'=>200,
                'html'=>$str,
            );
        }else{
            $data=array(
                'status'=>-1,
                'html'=>$apidata,
            );
        }
        $this->ajaxReturn($data);
    }
}
```
demo:[https://attigny.cn](https://attigny.cn)