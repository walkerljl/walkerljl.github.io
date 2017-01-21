---
layout : post
title : Elasticsearch入门
date : 2016-08-01
author : walkerljl
categories : blog
tag : Elasticsearch
---

# 一.介绍
Elasticsearch是一个基于Apache Lucene的开源搜索引擎。无论在开源还是专有领域，Lucene可以被认为是目前为止最先进、性能最好的、功能最全的搜索引擎库。

Lucene只是一个开发库。想要使用它，必须使用Java来作为开发语言并将其直接集成到你的应用中，不过Lucene非常复杂，需要深入了解检索的相关知识来理解它是如何工作的。

Elasticsearch也是使用Java开发并使用Lucene作为其核心来实现所有索引和搜索的功能，但是它的目的是通过简单的Restful API来隐藏Lucene的复杂性，从而让全文搜索变得更加简单。

Elasticsearch不仅仅是Lucene和全文搜索：

1. 分布式的实时文件存储，每个字段都被索引可被搜索。
2. 分布式的实时分析搜索引擎。
3. 可以扩展到上百条服务器，处理PB级结构化或非结构化数据。

而且上述所有功能都被集成到一个服务里面，你的应用可以功过简单的Restful API、各种语言的客户端甚至命令行与之交户。
# 二.下载、安装
1. 下载：<https://www.elastic.co/downloads/elasticsearch>
2. 安装：

	解压下载的文件到指定目录，如：~/workbench/tool/es/
3. 运行：

	~/workbench/tool/es/bin/elasticsearch（或在windows平台上运行\bin\elasticsearch.bat）
	
4. 测试：curl http://localhost:9200/

# 三.集群和节点
节点(node)是一个运行着的Elasticsearch实例。集群(cluster)是一组具有相同cluster.name的节点集合，他们协同工作，共享数据并提供故障转移和扩展功能，当然一个节点也可以组成一个集群。
你最好找一个合适的名字来替代cluster.name的默认值，比如你自己的名字，这样可以防止一个新启动的节点加入到相同网络中的另一个同名的集群中。
你可以通过修改config/目录下的elasticsearch.yml文件，然后重启ELasticsearch来做到这一点。当Elasticsearch在前台运行，可以使用Ctrl-C快捷键终止，或者你可以调用shutdown API来关闭：

curl -XPOST 'http://localhost:9200/_shutdown'
# 四.与Elasticsearch通信（交互）
如何与 Elasticsearch 通信要取决于你是否使用 JAVA。

1. JAVA API

如果你使用的是 JAVA，Elasticsearch 内置了两个客户端，你可以在你的代码中使用：

- 节点客户端: 节点客户端以一个 无数据节点 的身份加入了一个集群。换句话说，它自身是没有任何数据的，但是他知道什么数据在集群中的哪一个节点上，然后就可以请求转发到正确的节点上并进行连接。

- 传输客户端: 更加轻量的传输客户端可以被用来向远程集群发送请求。他并不加入集群本身，而是把请求转发到集群中的节点。

这两个客户端都使用 Elasticsearch 的 传输 协议，通过9300端口与 java 客户端进行通信。集群中的各个节点也是通过9300端口进行通信。如果这个端口被禁止了，那么你的节点们将不能组成一个集群。

>Java 的客户端的版本号必须要与 Elasticsearch 节点所用的版本号一样，不然他们之间可能无法识别。

2. 通过 HTTP 向 RESTful API 传送 json

其他的语言可以通过9200端口与 Elasticsearch 的 RESTful API 进行通信。事实上，如你所见，你甚至可以使用行命令 curl 来与 Elasticsearch 通信。

# 五.文档
程序中的对象很少是单纯的键值与数值的列表。更多的时候它拥有一个复杂的结构，比如包含了日期、地理位置、对象、数组等。

迟早你会把这些对象存储在数据库中。你会试图将这些丰富而又庞大的数据都放到一个由行与列组成的关系数据库中，然后你不得不根据每个字段的格式来调整数据，然后每次重建它你都要检索一遍数据。

Elasticsearch 是 面向文档型数据库，这意味着它存储的是整个对象或者 文档，它不但会存储它们，还会为他们建立索引，这样你就可以搜索他们了。你可以在 Elasticsearch 中索引、搜索、排序和过滤这些文档。不需要成行成列的数据。这将会是完全不同的一种面对数据的思考方式，这也是为什么 Elasticsearch 可以执行复杂的全文搜索的原因。

Elasticsearch使用 JSON (或称作JavaScript Object Notation ) 作为文档序列化的格式。JSON 已经被大多数语言支持，也成为 NoSQL 领域的一个标准格式。它简单、简洁、易于阅读。

# 六.基本概念介绍
1. 索引：对应关系型数据库中的数据库。
2. 类型：对应关系型数据库中的表。
3. 文档：对应关系型数据库中的行。
4. 字段：对应关系型数据库中的字段。

在Elasticsearch中，存储数据的行为叫作索引。文档属于一种类型，各种各样的类型存储在一个索引中。

一个Elasticsearch集群中可以包含多个索引，其中包含了很多类型。这些类型中包含了很多的文档，然后每个文档中又包含了很多的字段。

# 七.索引（创建索引、并添加数据）
```
curl -XPOST 'http://localhost:9200/identity/user_info/1?pretty' -d'{
	"id" : 1,
	"user_id" : "admin",
	"user_name" : "admin",
	"gender" : 1,
	"age" : 18,
	"interests" : ["写代码","听音乐"],
	"about" : "我喜欢攀登山峰。",
	"remark" : "",
	"status" : 1,
	"creator" : "admin",
	"created" : 1,
	"modifier" : "admin",
	"modified" : 1
}'
```
# 八.检索

1. 准备测试数据


```
curl -XPOST 'http://localhost:9200/identity/user_info/2?pretty' -d'{
	"id" : 2,
	"user_id" : "jarvis",
	"user_name" : "jarvis",
	"gender" : 1,
	"birthday" : "1990-01-01",
	"mobile" : "18000000000",
	"telephone" : "028-000000000",
	"email" : "jarvis@163.com",
	"address" : {"country" : "中国", "state" : "四川", "city" : "成都"},
	"interests" : ["学习","探索未知领域", "编码"],
	"about" : "我喜欢编程。",
	"remark" : "",
	"status" : 1,
	"creator" : "admin",
	"created" :2,
	"modifier" : "admin",
	"modified" : 2
}'

curl -XPOST 'http://localhost:9200/identity/user_info/3?pretty' -d'{
	"id" : 3,
	"user_id" : "walkerljl",
	"user_name" : "walkerljl",
	"gender" : 1,
	"birthday" : "2000-01-01",
	"mobile" : "18000000000",
	"telephone" : "028-000000000",
	"email" : "walkerljl@163.com",
	"address" : {"country" : "中国", "state" : "北京", "city" : "北京"},
	"interests" : ["学习","探索未知领域"],
	"about" : "我喜欢编故事。",
	"remark" : "",
	"status" : 1,
	"creator" : "admin",
	"created" : 3,
	"modifier" : "admin",
	"modified" : 3
}'

curl -XPOST 'http://localhost:9200/identity/user_info/4?pretty' -d'{
	"id" : 4,
	"user_id" : "walkerljl-1",
	"user_name" : "walkkerljl-1",
	"gender" : 2,
	"birthday" : "1980-01-01",
	"mobile" : "18000000000",
	"telephone" : "028-000000000",
	"email" : "walkerljl-1@163.com",
	"address" : {"country" : "中国", "state" : "重庆", "city" : "重庆"},
	"interests" : ["唱歌","旅游","游泳"],
	"about" : "我喜欢编织一些小玩意。",
	"remark" : "",
	"status" : 1,
	"creator" : "admin",
	"created" : 4,
	"modifier" : "admin",
	"modified" : 4
}'
```

2. 通过ID检索

```	
$ curl -XGET 'http://localhost:9200/identity/user_info／1?pretty'
```
> 通过ID检索

3. 简单的条件搜索

```
$ curl -XGET 'http://localhost:9200/identity/user_info/_search?pretty'
```
> 返回默认的前10个数据
	
```
$ curl -XGET 'http://localhost:9200/identity/user_info/_search?
	q=user_id:jarvis,pretty'
```
>检索“user_id”为“jarvis”的的信息。

4. 使用Query DSL检索

```
$ curl -XGET 'http://localhost:9200/identity/user_info/_search?pretty' -d '{
	"query" : {
        "match" : {
            "user_id" : "jarvis"
        }
    }
}'
```
> 通过Query DSL（Domain Specific Language 领域特定语言）可以完成更加复杂、强大的搜索任务。（需要通过JSON作为查询条件主体）

5. 更加复杂的检索
	
```
$ curl -XGET 'http://localhost:9200/identity/user_info/_search?pretty' -d '{
	"query" : {
        "filtered" : {
            "filter" : {
                "range" : {
                    "created" : { "gt" : 2 }
                }
            },
            "query" : {
                "match" : {
                    "gender" : "1" 
                }
            }
        }
    }
}'
```

6. 全文检索

```
$ curl -XGET 'http://localhost:9200/identity/user_info/_search?pretty' -d '{
    "query" : {
        "match" : {
            "about" : "编程"
        }
    }
}'
```

> 通常情况下，Elasticsearch 会通过相关性来排列顺序。
这个例子很好地解释了 Elasticsearch 是如何执行全文搜索的。对于 Elasticsearch 来说，相关性的概念是很重要的，而这也是它与传统数据库在返回匹配数据时最大的不同之处。

7. 段落检索

```
$ curl -XGET 'http://localhost:9200/identity/user_info/_search?pretty' -d '{
    "query" : {
        "match_phrase" : {
            "about" : "我喜欢编程"
        }
    }
}'
```	
>搜索about中只包含“我喜欢编程”短语的信息。

8. 高亮搜索结果

```
curl -XGET 'http://localhost:9200/identity/user_info/_search?pretty' -d '{
    "query" : {
        "match_phrase" : {
            "about" : "我喜欢编程"
        }
    },
    "highlight": {
        "fields" : {
            "about" : {}
        }
    }
}'
```
>很多程序希望能在搜索结果中 高亮 匹配到的关键字来告诉用户这个文档是 如何 匹配他们的搜索的。在 Elasticsearch 中找到高亮片段是非常容易的。

当我们运行这个查询后，相同的命中结果会被返回，但是我们会得到一个新的名叫 highlight 的部分。在这里包含了 about 字段中的匹配单词，并且会被 <em></em> HTML字符包裹住。

# 九.统计
1. 统计用户的兴趣爱好指数

```
$ curl -XGET 'http://localhost:9200/identity/user_info/_search?pretty' -d '{
     "aggs": {
    "all_interests": {
      "terms": { "field": "interests" }
    }
  }
}'
```

2. 根据条件统计用户的兴趣爱好指数

```
$ curl -XGET 'http://localhost:9200/identity/user_info/_search?pretty' -d '{
	"query": {
	    "match": {
	      "user_id": "jarvis"
	    }
	  },
     "aggs": {
    "all_interests": {
      "terms": { "field": "interests" }
    }
  }
}'
```

3. 统计每个兴趣爱好下的平均年龄

```
$ curl -XGET 'http://localhost:9200/identity/user_info/_search?pretty' -d '{
    "aggs" : {
        "all_interests" : {
            "terms" : { "field" : "interests" },
            "aggs" : {
                "avg_age" : {
                    "avg" : { "field" : "age" }
                }
            }
        }
    }
}
```

# 十.分布式
Elasticsearch可以被扩展到上百台（甚至上千台）服务器上，来处理PB级别的数据。Elasticsearch是自动分布的，它在设计时就考虑到隐藏分布操作的复杂性。

Elasticsearch分布式很简单。我们不需要关于分布式系统的任何任务，比如分片、集群、发现等成堆的分布式概念。在单个节点上和N个节点上运行，操作是完全一样的。

Elasticsearch很努力的避免复杂的分布式系统，很多操作都是自动完成：
- 可以将你的文档分区到不同容器或者分片中，这些文档可能被存储在一个节点或者多个节点。
- 跨界点平衡集群中节点间的索引和搜索负载
- 自动复制你的数据以提供冗余副本，防止硬件错误导致数据丢失。
- 自动在节点之间路由，以帮助你找到你想要的数据。
- 无缝扩展或恢复你的集群。

# 参考资料
1. <https://es.xiaoleilu.com>
2. <http://www.learnes.net>
