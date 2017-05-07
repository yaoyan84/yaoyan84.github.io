---
layout: post
title:  "ElasticSearch官方文档-探索你的集群"
date:   2017-05-07 09:12:05
categories: 
tags:  Elasticsearch  ELK
excerpt: ElasticSearch官方文档-探索你的集群翻译。
---

[原文地址](https://www.elastic.co/guide/en/elasticsearch/reference/5.3/_exploring_your_cluster.html)

## RSET API
现在我们的节点（和集群）已经启动运行了，下一步是理解如何与它交流。幸运的是ElasticSearch提供了一个非常完整强大的REST API，你能用来与你的集群交流。使用API你能完成如下一些事情：

* 检查你的集群、节点和索引的健康、状态和统计数据
* 管理你的集群、节点和索引数据和元数据
* 对你的索引执行CRUD（创建、读取、更新和删除）命令和搜索操作
* 执行诸如分页、排序、过滤、脚本、聚集等高级搜索操作

### Cluster Health 集群健康

```
GET /_cat/health?v        健康状态
GET /_cat/nodes?v         节点情况
```

健康状态有三种：

| 状态  | 含义                           |  
| ------ | -------------------------------------------------- | 
| green  | 一切正常（集群功能完整）                           |  
| yellow | 所有的数据都可用，但一些副本未分配（集群功能完整） | 
| red    | 某些原因一些数据不可用                             |    


**注意**： 就算集群状态是red，它也能提供部分的功能，比如它可以继续为可用的分片提供搜索请求的服务。但你在数据丢失以后，必须尽快的修复集群。

### 列出所有的索引

```
GET /_cat/indices?v
```

响应：

```
health   status    index                uuid                   pri    rep   docs.count docs.deleted store.size pri.store.size
yellow   open      .kibana              DYvIzeDiSpeWjuJX_U5yag  1      1         2         0           14.4kb      14.4kb
yellow   open      filebeat-2017.04.28  0EB4UTI-Shmct4wDHOuw0g  5      1     54618         0           23.5mb      23.5mb
```

### 创建索引

现在让我们创建一个名叫**customer**的索引，然后再次列出所有的索引：

```
PUT /customer?pretty
GET /_cat/indices?v
```

第一个命令使用PUT动作创建了一个名叫“customer”的索引。我们简单的附加一个“pretty”在末尾，告诉它采用“pretty-print”的JSON来响应（如果有的话）。

响应如下：

```
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   customer 95SQ4TSUT7mWBT7VNHH67A   5   1          0            0       260b           260b
```

结果的第二项告诉我们现在有1个索引名叫customer，它有5个主分片、1个副本（默认），且现在有0个文档在里面。

你也许也注意到了，customer索引有一个黄色的健康标记。回忆我们之前关于“黄色”状态的讨论，意味着一些副本还没有分配。原因是ElasticSearch默认的为这个索引创建1个副本。我们现在只有一个运行的节点，1个节点还不能分配（因为高可用的原因）直到一会儿另一个节点加入集群。从副本在第二个节点上被分配开始，这个索引的健康状态将变成“green”。

### 索引和查询一个文档

让我们现在放入一些东西在我们的customer索引。记住之前所说，为了索引一个文档，我们必须告诉elasticsearch将放入索引中的哪个类型里。

让我们索引一个简单的customer文档在 customer索引的“external”类型中，使用ID为1:

```json
PUT /customer/external/1?pretty
{ 
    "name": "John Doe"
}
```

返回：

```json
{  
    "_index" : "customer", 
	"_type" : "external", 
	"_id" : "1",  
	"_version" : 1, 
	"result" : "created", 
	"_shards" : {    
	    "total" : 2,   
		"successful" : 1,   
		"failed" : 0 
		},  
	"created" : true
}
```

从上面，我们能知道一个新的customer文档已经成功得在customer索引的external类别创建了。文档也有一个内建的ID为1，这是我们索引它时指定的。

**这点很主要**：ElasticSearch在你向一个索引索引文档之前不要求你明确的先创建一个索引。

在前面的例子中，如果之前没有创建customer索引，ElasticSearch将自动创建。

让我们现在检索我们刚索引的文档：

```
GET /customer/external/1?pretty
```

返回：

```json
{  
    "_index" : "customer", 
	"_type" : "external",  
	"_id" : "1",  
	"_version" : 1,  
	"found" : true, 
	"_source" : {
	    "name": "John Doe"
		}
}
```

### 删除索引

现在让我们删除我们刚刚创建的索引然后再次列出索引目录：

```
DELETE /customer?pretty
GET /_cat/indices?v
```

返回：

```
health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
```

这意味着索引已经被成功删除了，我们现在又回到了刚开始集群里是空的时候。
在我们继续之前，我们再仔细看看我们之前学到的几个API命令：

```
PUT /customer
PUT /customer/external/1{ 
    "name": "John Doe" }
GET /customer/external/1
DELETE /customer
```

如果我们仔细学习上面的命令，我们能切实的看到一个如何使用ElasticSearch传输数据的模式。这个模式可以总结如下：

```html
<REST Verb> /<Index>/<Type>/<ID>
```

这个REST传输模式遍及所有的API命令，如果你简单记住它，将是掌握ElasticSearch的一个好的开始。

