# ElasticSearch服务

### 一、基础知识

- 基于Lucene的搜索服务，提供了一个分布式多用户能力的全文搜索引擎，企业级的搜索引擎

- 之前怎么实现搜索的？SQL语句进行模糊查询，但是数据量过大而且慢，
- 理解阶段：可以看作是redis，它使用Java实现的，相当于一个web应用

### 二、环境准备

```shell
docker pull elasticsearch:7.6.2 #拉取镜像
docker run -d --name elasticsearch01 -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2#开启服务
docker stats 容器的id #查看占用的资源
curl 127.0.0.1:9200#进行访问
```

- 是后端应用，ElasticSearch-Head——可视化工具，监管ElasticSearch比如健康状态等。偏运维的工具

```shell
docker pull mobz/elasticsearch-head:5
docker run -d --name es_admin -p 9100:9100 mobz/elasticsearch-head:5

#在ElasticSearch中设置允许跨域,因为会有跨域的问题
docker exec -it 容器id /bin/bash
#config文件夹中的elasticsearch.yml文件
http.cors.enabled:true
thhp.cors.allow-origin:"*"

#解决数据浏览问题，进入ElasticSearch-Head
cd _site/
vim vendor.js
#进入6880行修改ajaxsettings
#contentType:"application/json;charset=UTF-8"
#在进入7500行
#同样的修改contentType
```

- 上面的可视化工具设置允许跨域后要保存退出重启
- kibana偏开发的工具

```shell
docker pull kibana:7.6.2
docker run -d --name kibana -e ELASTICSEARCH_HOSTS=http://10.0.2.15:9200 -p 5601:5601 kibana:7.6.2
```

### 三、ES核心概念

ElasticSearch是面向文档的。

|   DB    |       ES       |
| :-----: | :------------: |
| 数据库  |      索引      |
|   表    |   类型types    |
|  字段   |     fields     |
| 行 数据 | documents 文档 |

**节点和分片**

节点就是一个进程，主分片和从分片不在同一个节点中。

**倒排索引**

Lucence:倒排索引作为顶层，好处：快速的进行全文搜索。

将关键字作为key，将含有关键字的内容作为value。

比如下面这样的。

```
标签 			id
python 		1，2，3
redis		5 ,6
```

还有分值

**IK分词器**

可以进行中文分词，ik_smart 最少切分，ik_max_word 最大单词切分，最细粒度切分，穷举

```shell
root@DF:/home/bd# docker cp ./elasticsearch-analysis-ik-7.6.2.zip ea3c72ce5a91:/usr/share/elasticsearch/pluginsroot@DF:/home/bd# docker exec -it ea3c72ce5a91 /bin/bash
[root@ea3c72ce5a91 elasticsearch]# cd plugins/
[root@ea3c72ce5a91 plugins]# ls
elasticsearch-analysis-ik-7.6.2.zip
[root@ea3c72ce5a91 plugins]# mkdir ik
[root@ea3c72ce5a91 plugins]# ls
elasticsearch-analysis-ik-7.6.2.zip  ik
[root@ea3c72ce5a91 plugins]# mv elasticsearch-analysis-ik-7.6.2.zip ./ik/
[root@ea3c72ce5a91 plugins]# ls
ik
[root@ea3c72ce5a91 plugins]# cd ik
[root@ea3c72ce5a91 ik]# unzip elasticsearch-analysis-ik-7.6.2.zip
Archive:  elasticsearch-analysis-ik-7.6.2.zip
   creating: config/
  inflating: config/main.dic
  inflating: config/quantifier.dic
  inflating: config/extra_single_word_full.dic
  inflating: config/IKAnalyzer.cfg.xml
  inflating: config/surname.dic
  inflating: config/suffix.dic
  inflating: config/stopword.dic
  inflating: config/extra_main.dic
  inflating: config/extra_stopword.dic
  inflating: config/preposition.dic
  inflating: config/extra_single_word_low_freq.dic
  inflating: config/extra_single_word.dic
  inflating: elasticsearch-analysis-ik-7.6.2.jar
  inflating: httpclient-4.5.2.jar
  inflating: httpcore-4.4.4.jar
  inflating: commons-logging-1.2.jar
  inflating: commons-codec-1.9.jar
  inflating: plugin-descriptor.properties
  inflating: plugin-security.policy
[root@ea3c72ce5a91 ik]# rm -rf elasticsearch-analysis-ik-7.6.2.zip

```



### 四、kibana的使用

**路由**：/索引/类型/文档——增加drfstudy索引里面的product类型里面的文档为id的文档数据。

增加drfstudy库里面product表里面的一条数据，增加一条文档数据：put/drfstudy/product/1

#### 4.1简单使用

```python
#分词测试
GET _analyze
{
  "analyzer": "ik_smart"
  , "text": "一个真帅"
}
GET _analyze
{
  "analyzer": "ik_max_word",
  "text": "郡国国真厉害"
}

#创建或修改指定文档数据
PUT /test1/_doc/1
{
  "name":"yige",
  "age":28
}
#获取指定文档数据
GET /test1/_doc/1

#创建索引,就像创建表
PUT /test2
{
  "mappings": {
    "properties": {
      "name":{
        "type": "text"
      },
      "age":{
        "type": "integer"
      }
    }
  }
}
#查看索引
GET /test2
```

#### 4.2进阶使用

```python
#删除索引
DELETE /test2

#添加文档
PUT /product/_doc/4
{
  "title":"针织帽",
  "price":50,
  "desc":"保暖",
  "tag":["服装","生活"]
}
#查询
GET /product/_doc/1


#修改,修改全部用put，部分修改用post
POST /product/_doc/1/_update
{
  "doc":{
    "title":"小米手机修改"
  }
}

#复杂查询：
#逻辑查询：多条件查询
#must 等于and
GET /product/_doc/_search
{
  "query":{
    "bool":{
      "must":[
        {
          "match":{
            "title":"手机"
          }
        },
        {
          "match":{
            "price":1999
          }
        }
        ]
    }
  }
}


#should 等于 or
GET /product/_doc/_search
{
  "query":{
    "bool":{
      "should":[
        {
          "match":{
            "title":"手机"
          }
        },
        {
          "match":{
            "price":8000
          }
        }
        ]
    }
  }
}
#must_not 等于 not
GET /product/_doc/_search
{
  "query":{
    "bool":{
      "must_not":[
        {
          "match":{
            "title":"手机"
          }
        }
        ]
    }
  }
}

#字段过滤
GET /product/_doc/_search
{
  "query":{
    "match":{
      "title":"机"
    }
  },
  "_source":["title","price"]
}

#比较查询，gte e等于，gt大于，lt小于
GET /product/_doc/_search
{
 "query":{
   "bool":{
     "must":[
       
       {
         "match":{
           "title":"手机"
         }
       }
       ],
       "filter":{
         "range":{
           "price":{
             "gte":4000,
             "lte":6000
           }
         }
       }
   }
 }
}
```

