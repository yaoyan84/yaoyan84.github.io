---
layout: post
title:  "Elasticsearch基本概念"
date:   2017-04-27 22:12:05
categories: 
tags:  Elasticsearch  ELK
excerpt: Elasticsearch基本概念官方文档的翻译。
---

[原文地址](https://www.elastic.co/guide/en/elasticsearch/reference/5.3/_basic_concepts.html)

Elasticsearch Reference [5.3] » Getting Started » Basic Concepts

## Basic Concepts 基本概念

>There are a few concepts that are core to Elasticsearch. Understanding these concepts from the outset will tremendously help ease the learning process.

这有一些ElasticSearch的核心概念，开始就理解这些概念将对学习进程有极大的帮助。

### Near Realtime (NRT) 准实时

>Elasticsearch is a near real time search platform. What this means is there is a slight latency (normally one second) from the time you index a document until the time it becomes searchable.

Elasticsearch是一个准实时的搜索平台。这意味着从你索引一个文档到它可以搜搜的时间的延迟很短（正常一秒）

### Cluster  集群

>A cluster is a collection of one or more nodes (servers) that together holds your entire data and provides federated indexing and search capabilities across all nodes. A cluster is identified by a unique name which by default is "elasticsearch". This name is important because a node can only be part of a cluster if the node is set up to join the cluster by its name.

一个集群是一个或多个节点（服务）的集合，它们一起保存你的整个数据并通过所有节点提供索引和搜索的能力。一个集群由一个唯一的名字来标识，默认值是"elasticsearch"。这个名字非常重要，一个节点设置它的集群名字来加入一个集群，一个节点只能属于一个集群。

>Make sure that you don’t reuse the same cluster names in different environments, otherwise you might end up with nodes joining the wrong cluster. For instance you could use logging-dev, logging-stage, and logging-prod for the development, staging, and production clusters.

确保你没有在不同的环境重复使用同样的集群名字，否则你将使节点加入错误的集群。例如你可以使用 logging-dev, logging-stage 和 logging-prod 分别作为开发环境、模拟环境和生产环境的集群名。

>Note that it is valid and perfectly fine to have a cluster with only a single node in it. Furthermore, you may also have multiple independent clusters each with its own unique cluster name.

注意一个集群只有一个单独的节点在其中，是完全合法的。于此同时你的多个独立的集群要有自己唯一不重复的名字。

### Node  节点

>A node is a single server that is part of your cluster, stores your data, and participates in the cluster’s indexing and search capabilities. Just like a cluster, a node is identified by a name which by default is a random Universally Unique IDentifier (UUID) that is assigned to the node at startup. 

一个节点是你的集群中的一个单独的服务，存储你的数据，并参与形成集群的索引和搜索能力。与一个集群类似，一个节点也被一个名字所标识，默认值是启动时分配一个随机的UUID。

>You can define any node name you want if you do not want the default. This name is important for administration purposes where you want to identify which servers in your network correspond to which nodes in your Elasticsearch cluster.

如果你不想要默认的，你可以定义任何你想要的节点名字。这个名字对你想要标识你的ElasticSearch集群中的节点是你网络中的哪个服务器这样一些管理目的比较重要。

>A node can be configured to join a specific cluster by the cluster name. By default, each node is set up to join a cluster named elasticsearch which means that if you start up a number of nodes on your network and—assuming they can discover each other—they will all automatically form and join a single cluster named elasticsearch.

一个节点能被配置根据集群名称加入一个特定的集群。默认的每个节点都设置为加入名字为elasticsearch的集群，这意味着如果你在你的网络中启动若干节点并且假定他们能互相发现对方，它们全部将自动的形成一个单独的名叫elasticsearch的集群。

>In a single cluster, you can have as many nodes as you want. Furthermore, if there are no other Elasticsearch nodes currently running on your network, starting a single node will by default form a new single-node cluster named elasticsearch.

在一个单独的集群，你想要有多少个节点都可以。而且如果没有其他节点正在你的网络运行，启动一个节点将默认的形成一个单节点的名叫elasticsearch的集群。

### Index  索引

>An index is a collection of documents that have somewhat similar characteristics. 

一个索引是一个有某种程度相似特性的文档的集合。

>For example, you can have an index for customer data, another index for a product catalog, and yet another index for order data. 

例如，你可以有一个客户数据的索引，另一个产品目录的索引，还有一个订单数据的索引。

>An index is identified by a name (that must be all lowercase) and this name is used to refer to the index when performing indexing, search, update, and delete operations against the documents in it.

一个索引由一个名字标识（必须全是小写字母），此名称用于在对文档进行索引、搜索、更新和删除操作时引用索引。

>In a single cluster, you can define as many indexes as you want.

在一个单独的集群，你如果愿意，能定义任意个索引。

### Type 类型

>Within an index, you can define one or more types. A type is a logical category/partition of your index whose semantics is completely up to you. In general, a type is defined for documents that have a set of common fields. For example, let’s assume you run a blogging platform and store all your data in a single index. In this index, you may define a type for user data, another type for blog data, and yet another type for comments data.

在索引中，可以定义一个或多个类型。 一个类型是完全由你来定义的索引的一个逻辑分类/分区。一般的，一个类型为有公共字段的文档定义的。例如，让我们假定你运行了一个博客平台并且存储你所有的数据在一个索引里，在这个索引里，你可以为用户数据顶一个一个类型，为博客数据定义一个类型，再为评论定义一个类型。

### Document 文档

>A document is a basic unit of information that can be indexed. For example, you can have a document for a single customer, another document for a single product, and yet another for a single order. This document is expressed in JSON (JavaScript Object Notation) which is an ubiquitous internet data interchange format.
Within an index/type, you can store as many documents as you want. Note that although a document physically resides in an index, a document actually must be indexed/assigned to a type inside an index.

一个文档是一个能被索引的信息的基本单位。例如，你能将一个单独的客户作为一个文档，一个单独的产品作为另一个文档，再把一个单独的订单当作一个文档。这个文档使用无处不在的护粮网数据交换格式JSON来表达。
在一个索引/类型中，你能想存储多少个文档都可以。注意尽管一个文档物理上属于一个索引，但必须索引分配到索引的类型中。

### Shards & Replicas  分片 & 副本

>An index can potentially store a large amount of data that can exceed the hardware limits of a single node. 
For example, a single index of a billion documents taking up 1TB of disk space may not fit on the disk of a single node or may be too slow to serve search requests from a single node alone.
To solve this problem, Elasticsearch provides the ability to subdivide your index into multiple pieces called shards.

例如，十亿个文件占用磁盘空间1TB的单索引可能不适合单节点的磁盘或仅从单个节点的搜索请求可能服务太慢。索引可以存储大量的数据，这些数据可能超过单个节点的硬件限制。
为了解决这个问题，ElasticSearch提供了将索引拆分成多个碎片的功能，叫做分片。

>When you create an index, you can simply define the number of shards that you want. 
Each shard is in itself a fully-functional and independent "index" that can be hosted on any node in the cluster.

当你创建索引时，你能简单的定义你想要的分片数量。
每个碎片本身是一个全功能的、独立的“索引”，可以托管在集群中的任何节点。

>Sharding is important for two primary reasons:
> * It allows you to horizontally split/scale your content volume
> * It allows you to distribute and parallelize operations across shards (potentially on multiple nodes) thus increasing performance/throughput
  
分片很重要的两个主要原因
  * 它允许你横向的拆分扩展你的内容卷
  * 它允许你通过可能在多节点的分片并行分配操作，提高性能表现和业务能力

>The mechanics of how a shard is distributed and also how its documents are aggregated back into search requests are completely managed by Elasticsearch and is transparent to you as the user.

技术上如何实现分片分配和如果将文档集合返回给搜索请求是由ElasticSearch管理实现，对于用户来说是透明的。

>In a network/cloud environment where failures can be expected anytime, it is very useful and highly recommended to have a failover mechanism in case a shard/node somehow goes offline or disappears for whatever reason. 
To this end, Elasticsearch allows you to make one or more copies of your index’s shards into what are called replica shards, or replicas for short.

在一个网络或者云环境下，失败任何时候都可能发生，在有一个分片或节点因任何原因掉线或消失时有一个故障转移机制是非常有用并强烈推荐的。
为此，ElasticSearch允许你为你的索引分片生成一个或多个拷贝，成为副本分片，简称副本。

>Replication is important for two primary reasons:
> * It provides high availability in case a shard/node fails. For this reason, it is important to note that a replica shard is never allocated on the same node as the original/primary shard that it was copied from.
> * It allows you to scale out your search volume/throughput since searches can be executed on all replicas in parallel.

复制很重要的两个主要原因
  * 在一个分片或节点宕掉时，它提供高可用。为此，重点注意副本分片不能与源/主分片存放在一个节点上。
  * 它允许你扩展你的搜索节点，搜索也可以并行在副本上进行。


>To summarize, each index can be split into multiple shards. An index can also be replicated zero (meaning no replicas) or more times. Once replicated, each index will have primary shards (the original shards that were replicated from) and replica shards (the copies of the primary shards). 
The number of shards and replicas can be defined per index at the time the index is created. After the index is created, you may change the number of replicas dynamically anytime but you cannot change the number of shards after-the-fact.

总之，每个索引能被分成多个分片。一个索引也能被复制0（意味着没有副本）或多次。一旦复制了，每个索引有主分片（复制的源分片）和副本分片（主分片的拷贝）。
分片和副本的数量，能在每一个索引创建的时候定义。在索引创建之后，你能随时动态地改变副本的数量，但是你不能事后改变分片的数量。

>By default, each index in Elasticsearch is allocated 5 primary shards and 1 replica which means that if you have at least two nodes in your cluster, your index will have 5 primary shards and another 5 replica shards (1 complete replica) for a total of 10 shards per index.

默认的，每个ElasticSearch的索引分配5个主分片和1个副本，意味着你至少有两个节点在集群里，你的索引将有5个主分片和5个副本分片（一个完整的副本），每个索引将总共有10个分片。

>Each Elasticsearch shard is a Lucene index. There is a maximum number of documents you can have in a single Lucene index. As of LUCENE-5843, the limit is 2,147,483,519 (= Integer.MAX_VALUE - 128) documents. You can monitor shard sizes using the _cat/shards api.

每个ElasticSearch的分片是一个Lucene索引。在一个单独的Lucene索引中你能存放的文档有最大数量上限。根据 LUCENE-5843，极限是2147483519（= integer.max_value - 128）个文档。你能使用_cat/shards 接口监控分片的大小。

>With that out of the way, let’s get started with the fun part…
