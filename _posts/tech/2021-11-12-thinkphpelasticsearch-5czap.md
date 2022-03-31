---
layout: post
title: ThinkPHP使用Elasticsearch分词搜索
category: PHP
keywords: 
tags: 常识 PHP
description: 
---

> Elasticsearch使用Java环境所以需要先搭建Java环境   
>环境CentOS 7.6、Java10、Elasticsearch7.15.2
#### 安装 Java环境
1、获取RPM安装包    
```
wget http://file.alonesky.com/file/soft/jdk-10.0.2_linux-x64_bin.rpm
```
2、安装RPM包
```
sudo rpm -ivh jdk-10.0.2_linux-x64_bin.rpm
```
3、查看Java版本号(有版本号则表示安装成功)
```
java -version
```
#### 配置系统变量
1、编辑系统变量
``` 
vi /etc/profile
// 文件末尾追加(Java路径不一致需修改)
export JAVA_HOME=/usr/java/jdk-10.0.2
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
```
2、写入到缓存
```
source /etc/profile
```
#### 安装Elasticsearch
1、获取RPM安装包    
```
wget http://file.alonesky.com/file/soft/elasticsearch-7.15.2-x86_64.rpm
```
2、安装RPM包
```
sudo rpm -ivh elasticsearch-7.15.2-x86_64.rpm
```
3、编辑Elasticsearch配置文件
```
vi /etc/elasticsearch/elasticsearch.yml
// 取消以下几行注释
# 锁定内存
bootstrap.memory_lock: true 
# 本地访问 可改为0.0.0.0 外网访问
network.host: localhost 
# 端口
http.port: 9200
```
> 至此Elasticsearch已经安装完成
#### 启动Elasticsearch服务
```
# 重载某个服务的配置文件
sudo systemctl daemon-reload
# 启用elasticsearch服务
sudo systemctl enable elasticsearch.service
# 开启elasticsearch
sudo systemctl start elasticsearch
```
> Elasticsearch 启动的慢稍等一会
#### Elasticsearch不能启动处理
1、查看端口,检查 9200 端口是否成功启动
```
netstat -plntu
```
2、启动不起来可能是内存不够修改配置
```
vi /etc/elasticsearch/jvm.options
```
3、重启Elasticsearch服务
```
sudo systemctl restart elasticsearch
```
4、测试
```
curl localhost:9200
curl 外网IP:9200
```
#### 安装中文分词
1、安装分词扩展
```
/usr/share/elasticsearch/bin/elasticsearch-plugin install https://file.alonesky.com/file/soft/elasticsearch-analysis-ik-7.15.2.zip
```
2、重新启动Elasticsearch服务
```
sudo systemctl restart elasticsearch
```
3、查看ik-analyzer的效果
```
curl -H 'Content-Type: application/json'  -XGET 'localhost:9200/_analyze?pretty' -d '{"analyzer":"ik_max_word","text":"今天天气真好"}'
```
4、增加自定义词库
```
vi /etc/elasticsearch/analysis-ik/IKAnalyzer.cfg.xml
// 在 ext_dict 中自定义一个词库文件 keywords.dic;
echo '日出的幻景' > /etc/elasticsearch/analysis-ik/keywords.dic
```
5、重新启动Elasticsearch服务
 ```
 sudo systemctl restart elasticsearch
 ```
#### 代码安装Elasticsearch扩展
```
composer require elasticsearch/elasticsearch ~6.5.0
```
#### 插入索引
```
$elasticsearchModel = new Elasticsearch();
$s_status = db('shop')->where('id', $shop_id)->value('status');
$is_clearkucun = db('shop')->where('id', $shop_id)->value('is_clearkucun');
$status = [
    'is_clearkucun' => $is_clearkucun,
    's_status' => $s_status,
    'status' => 0,
    'status_f' => 0,
    'on_sale' => 0,
];
$elasticsearchModel->insertIndexMore($goods_id, $name, $maidian, $content, $status);
```
#### 更新索引
```
$elasticsearchModel = new Elasticsearch();
$s_status = db('shop')->where('id', $shop_id)->value('status');
$is_clearkucun = db('shop')->where('id', $shop_id)->value('is_clearkucun');
$goods_info = db('goods')->where('id', $goods_id)->field('name,maidian,content,status,status_f,on_sale')->find();
$status = [
    'is_clearkucun' => $is_clearkucun,
    's_status' => $s_status,
    'status' => input('status',0),
    'status_f' => input('status_f',0),
    'on_sale' => input('on_sale',0),
];
$elasticsearchModel->deleteIndex($goods_id);
$elasticsearchModel->insertIndexMore($goods_id, $goods_info['name'], $goods_info['maidian'], $goods_info['content'], $status);
```
#### 删除索引
```
$elasticsearchModel = new Elasticsearch();
$elasticsearchModel->deleteIndex($goods_id);
```
#### 使用搜索
```
$elasticsearchModel = new Elasticsearch();
$response = $elasticsearchModel->search($key_word, $page, $limit);
// dump($response);
if (!empty($response)) {
    $search_goods_id = array_column($response, '_id');
    // dump($search_goods_id);
    $where = ['id' => $search_goods_id];
    $goods_ids_str = implode(',', $search_goods_id);
    $order = "field(id," . $goods_ids_str . ")";
} else {
    $where= ['name', 'like', '%' . $key_word . '%'];
    $order = "sort desc";
}
$goods_list = db('goods')
    ->where($where)
    ->field('id,name,good_number,image,hot as sale,price')
    ->orderRaw($order)
    ->select();
```
附件下载：[https://file.alonesky.com/file/soft/Elasticsearch.tar.gz](https://file.alonesky.com/file/soft/Elasticsearch.tar.gz)    
参考链接：[http://blog.huati365.com/7a46ca39c9ab3b8a](http://blog.huati365.com/7a46ca39c9ab3b8a)