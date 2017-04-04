---
layout: post
title:  "清除MySql-bin日志"
date:   2011-06-02 20:06:05
categories: Web
tags: php 分页
excerpt: 清除MySql-bin日志。
---

Mysql跑久了，会产生很多mysql-bin.(数字) 之类的日志文件，会占用很多空间。
如图：

![](/images/posts/mysql-bin_thumb.jpg)

删除这些日志文件：

用 ```mysql -u root –p``` 输入密码进入mysql命令行，执行下面命令清除指定日期之前的日志

```
PURGE MASTER LOGS before ’2011-6-1′;
```
 
如下：

```
mysql> PURGE MASTER LOGS before ’2011-6-1′; 
Query OK, 0 rows affected (0.68 sec)
```