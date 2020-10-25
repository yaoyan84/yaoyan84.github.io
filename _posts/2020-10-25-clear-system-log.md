---
layout: post
title:  "处理messages日志占用var过大问题"
date:   2020-10-25 11:23:34
categories: 
tags: Linux
excerpt: 
---

收到通知，服务器有空间使用量报警：

**/var空间使用量超过85%**

检查发现messages从5月份到现在10月份都没有正确切割，所以日志过大。

没有正确切割日志，确认是logrotate配置文件权限问题引起。修复了权限问题，增加了一些logrotate参数，但仍要处理当前服务器的var空间问题。

找到messages日志中，10月份第1天第1行

```bash
grep -n ^Oct /var/log/messages | head -1
```

如找到是 ```432111235``` 行。

删除从第1行到这行之前的所有内容。

```bash
sed -i '1,432111234d' /var/log/messages
```

检查发现文件大小已变小，但 ```df``` 查看var空间占用没有减少反而增大了点。

因为/var/log/messages一直有进程占用，所以实际并未释放空间。

重启日志服务：

```bash
systemctl restart rsyslog
```

再次查看var空间已经释放。
