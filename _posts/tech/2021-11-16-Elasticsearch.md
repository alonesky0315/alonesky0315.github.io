---
layout: post
title: ThinkPHP使用Elasticsearch分词搜索
category: 技术
keywords: ThinkPHP,基础教程,Elasticsearch,分词搜索
tags: ThinkPHP,基础教程,Elasticsearch,分词搜索
description: ThinkPHP使用Elasticsearch分词搜索
---

> Elasticsearch使用Java环境所以需要先搭建Java环境   
> 环境CentOS 7.6、Java10、Elasticsearch
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
bootstrap.memory_lock: true
network.host: localhost
http.port: 9200
```
> 至此Elasticsearch已经安装完成
#### 启动Elasticsearch服务
```
sudo systemctl daemon-reload
sudo systemctl enable elasticsearch.service
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
4、查看端口,检查 9200 端口是否成功启动
```
netstat -plntu
```
5、测试
```
curl localhost:9200
```
#### 安装中文分词
1、安装分词扩展
```
/usr/share/elasticsearch/bin/elasticsearch-plugin install https://file.alonesky.com/file/soft/elasticsearch-analysis-ik-7.15.1.zip
```
2、重新启动Elasticsearch服务
```
sudo systemctl restart elasticsearch
```
3、查看端口,检查 9200 端口是否成功启动
```
netstat -plntu
```
4、查看ik-analyzer的效果
```
curl -H 'Content-Type: application/json'  -XGET 'localhost:9200/_analyze?pretty' -d '{"analyzer":"ik_max_word","text":"今天天气真好"}'
```
5、增加自定义词库
```
vi /etc/elasticsearch/analysis-ik/IKAnalyzer.cfg.xml
// 在 ext_dict 中自定义一个词库文件 keywords.dic;
echo '日出的幻景' > /etc/elasticsearch/analysis-ik/keywords.dic
```
6、重新启动Elasticsearch服务
 ```
 sudo systemctl restart elasticsearch
 ```
7、查看端口,检查 9200 端口是否成功启动
 ```
 netstat -plntu
 ```
#### 安装Elasticsearch扩展
```
composer require elasticsearch/elasticsearch ~6.5.0
```
#### Elasticsearch类
```
namespace app\common\model;

use Elasticsearch\ClientBuilder;
use think\Model;

/**
 * Class Elasticsearch
 * @package app\common\model
 */
class Elasticsearch extends Model
{
    /*
     * index  相当于 数据库
     * type  相当于 数据表
     * document  相当于 一行数据
     * id  document的唯一标识
     * 查询关键字：
     * must  与
     * must not 排除
     * should  或
     * must    查询内容必须出现在匹配的文档中，并将有助于得分；
     * must_not    查询内容不能出现在匹配的文档中；
     * filter    查询内容必须出现在匹配的文档中，是对匹配的结果过滤，在过滤器上下文中执行，意味着评分被忽略；
     * should    查询内容应该出现在匹配的文档中，至少有一个；
     * */
    protected $client;
    protected $params;

    protected function initialize()
    {
        $this->params = [
            'index' => 'guoxin',
            'type' => 'goods'
        ];
        $this->client = ClientBuilder::create()->setHosts(
            [
                'host' => '127.0.0.1',
                'port' => '9200',
            ])->build();
    }

    /**
     * 生成索引
     * @param int $goods_id
     * @param string $title
     * @param string $maidian
     * @param string $content
     * @return
     */
    public function insertIndex($goods_id = 0, $title = '', $maidian = '', $content = '')
    {
        //插入数据
        $params = array_merge($this->params, [
            'id' => $goods_id,
            'body' => [
                'id' => $goods_id,
                'title' => $title,
                'maidian' => $maidian,
                'content' => $content,
                'create_time' => time(),
            ],
        ]);
        return $this->client->index($params);
    }

    /**
     * 生成索引
     * @param int $goods_id
     * @param string $title
     * @param string $maidian
     * @param string $content
     * @param $status
     * @return
     */
    public function insertIndexMore($goods_id = 0, $title = '', $maidian = '', $content = '', $status)
    {
        //插入数据
        $params = array_merge($this->params, [
            'id' => $goods_id,
            'body' => [
                'id' => $goods_id,
                'title' => $title,
                'maidian' => $maidian,
                'content' => $content,
                'is_clearkucun' => $status['is_clearkucun'],
                's_status' => $status['s_status'],
                'status' => $status['status'],
                'status_f' => $status['status_f'],
                'on_sale' => $status['on_sale'],
                'create_time' => time(),
            ],
        ]);
        return $this->client->index($params);
    }

    /**
     * 获取索引
     * @param int $goods_id
     * @return mixed|void
     */
    public function getIndex($goods_id = 0)
    {
        $params = array_merge($this->params, [
            'id' => $goods_id,
        ]);
        //获取指定的文档
        $response = $this->client->get($params);
        //获取指定的文档 数据
        // $response = $this->client->getSource($params);
        return $response;
    }

    /*
      * 设置索引
      * return
      */
    public function setIndex()
    {
        $params = [
            'index' => 'guoxin',
            'body' => [
                'settings' => [
                    'number_of_shards' => 2,
                    'number_of_replicas' => 0
                ],
            ],
        ];
        $this->client->indices()->create($params);
    }

    /**
     * 删除索引
     * @param int $goods_id
     * @return mixed
     */
    public function deleteIndex($goods_id = 0)
    {
        $params = array_merge($this->params, [
            'id' => $goods_id,
        ]);
        // 删除指定的文档
        $response = $this->client->delete($params);
        return $response;
    }

    /**
     * 更新索引
     * @param int $goods_id
     * @param string $title
     * @param string $maidian
     * @param string $content
     * @param $status
     * @return mixed
     */
    public function updateIndex($goods_id = 0, $title = '', $maidian = '', $content = '', $status)
    {
        $params = array_merge($this->params, [
            'id' => $goods_id,
            'body' => [
                'title' => $title,
                'maidian' => $maidian,
                'content' => $content,
                'is_clearkucun' => $status['is_clearkucun'],
                's_status' => $status['s_status'],
                'status' => $status['status'],
                'status_f' => $status['status_f'],
                'on_sale' => $status['on_sale'],
                'create_time' => time(),
            ],
        ]);
        // dump($params);exit;
        // 更新指定的文档
        $response = $this->client->update($params);
        return $response;
    }

    /**
     * 搜索某一字段
     * @param string $title
     * @param $page
     * @param int $size
     * @return mixed
     */
    public function search($title = '', $page, $size = 10)
    {
        $params = array_merge($this->params, [
            'body' => [
                'query' => [
                    'bool' => [
                        'must' => [
                            [
                                'match' => [
                                    'title' => [
                                        'query' => $title,
                                        'fuzziness' => 'auto',
                                        'minimum_should_match' => '30%',
                                    ],
                                ]
                            ],
                            ['match' => ['is_clearkucun' => 1]],
                            ['match' => ['s_status' => 1]],
                            ['match' => ['status' => 1]],
                            ['match' => ['status_f' => 1]],
                            ['match' => ['on_sale' => 1]],
                        ],
                        'should' => [
                            'match_phrase' => [
                                'title' => [
                                    'query' => $title,
                                    'slop' => 50,
                                ]
                            ]
                        ],
                    ]
                ],
                'from' => '0',
                'size' => '10000',
                'sort' => [
                    // 对create_time字段进行降序排序
                    '_score' => 'desc'
                ]
            ],
        ]);
        $response = $this->client->search($params);   //搜索关键词
        // dump($response);exit;
        $start = ($page - 1) * $size;
        return array_slice($response['hits']['hits'], $start, $size);
        // return $response;
    }

    /**
     * @return mixed
     */
    public function index()
    {
        $params = array_merge($this->params, [
            'client' => [
                'ignore' => 404
            ]
        ]);
        // $response = $client->indices()->delete($params);         // 删除库索引
        // $response = $client->indices()->getSettings($params);    // 获取库索引设置信息
        // $response = $this->client->indices()->exists($params);   // 检测库是否存在
        $response = $this->client->indices()->getMapping($params);  // 获取mapping信息
        return $response;
    }

    /**
     * 搜索多个字段
     * query bool must 是 and 查询
     * @param string $title
     * @param string $maidian
     * @param string $content
     * @return
     */
    public function search_more($title = '', $maidian = '', $content = '')
    {
        $params = array_merge($this->params, [
            'body' => [
                'query' => [
                    'bool' => [
                        'must' => [
                            'match' => [
                                'title' => $title,
                                'maidian' => $maidian,
                                'content' => $content
                            ],
                        ]
                    ]
                ],
                'from' => '0',
                'size' => '200'
            ]
        ]);
        $response = $this->client->search($params);   //搜索 指定的文档
        return $response;
    }
}
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
$elasticsearchModel->deleteIndex($goods_id);
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
参考链接：[http://blog.huati365.com/7a46ca39c9ab3b8a](http://blog.huati365.com/7a46ca39c9ab3b8a)

原文链接：[https://blog.alonesky.com/thinkphpelasticsearch-5czap](https://blog.alonesky.com/thinkphpelasticsearch-5czap)

