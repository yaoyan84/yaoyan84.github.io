---
layout: post
title:  "Redis monitor"
date:   2017-04-16 10:30:05
categories: 
tags:  redis
excerpt: 关于redis monitor。
---

## 关于redis monitor

最近在找redis的监控工具，在网上看到有人推荐[RedisLive](https://github.com/nkrode/RedisLive)和[redis-monitor](https://github.com/LittlePeng/redis-monitor)。redis-monitor是由redis-monitor fork而来，增加了OverView，而且还做了报警接口，很不错。先star再弄下来捣鼓捣鼓。

![Redis Live](https://raw.github.com/yaoyan84/redis-monitor/master/design/overview.png)

## 下载启动

[redis-monitor](https://github.com/LittlePeng/redis-monitor)的使用方法超级简单，下载，然后修改 **src/redis_live.conf** ，然后按照说明启动就可以用了。

文件说明：

  * redis_live.py 启动网页服务，默认提供0.0.0.0:8888的http服务，就是我们看到的监控界面，前台方式，在控制台输出access；
  * redis_monitor.py 监控采集程序，启动多线程定时连接各redis采集情报，并保存，前台方式，控制台输出；
  * redis_live_daemon.py、redis_monitor_daemon.py 分别为启动web服务和监控采集的守护模式；

## 问题和解决

在使用中还是遇到了一些问题，开启调试模式，根据自己的需要对代码进行了一些修改解决了一些。也还有一些问题因为并不影响自己用，没有仔细去看。[修改后的代码](https://github.com/yaoyan84/redis-monitor)：

  * 储存监控采集的历史数据智能用redis，修改 **DataStoreType** 为sqlite时候报错，反正用redis也很简单，无所谓；
  * 存储监控数据的redis有密码时候，在 **RedisStatsServer** 中添加password也让然报验证失败的错误。监控数据用的redis没有密码也不是很要紧；
  * 网页在查看Live时，趋势图上的一排小方格里的状态不显示任何东西，检查发现access里有接口调用500了，最终确认是因为被监控的redis都有密码的原因，修改了相应接口文件，从配置文件中读密码加上；
  * 调整了一下QverView界面：master节点的slave显示优化；detail使用一个可隐藏的图层来显示，感觉看着更好点。
  * 修复了monitor报错：```TypeError: cannot concatenate 'str' and 'dict' objects ```报错，修改了主节点获取slaves信息代码，先转个格式;
  * live界面的Replication总觉得显示别扭，要不就不显示，可能我修复上面那个slaves信息代码时候引起了别的问题，先去掉，在OverView里可以快速的看到副本关系。

经过短时间的调整修改，基本这个工具已经可以使用了。

PS：利用sms报警调用接口，可修改成与其他运管平台报警事件对接。有需要时候再捣鼓吧。




