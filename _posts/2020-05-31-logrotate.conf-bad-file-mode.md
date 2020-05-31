---
layout: post
title:  "logrotate配置文件权限问题"
date:   2020-05-31 17:30:05
categories: 
tags:  linux logrotate
excerpt: 
---

最近发现个别服务器修改了logrotate设置后，并没有生效，检查各种配置，未发现问题。后手动执行时，看到控制台有报错：

```
# /usr/sbin/logrotate -v /etc/logrotate.conf
Ignoring /etc/logrotate.conf because of bad file mode.
```

经查发现为 `/etc/logrotate.conf` 配置文件的权限问题引起。

经过检查，发现这个配置文件的权限不对，是664，而不是644。

```bash
chmod 644 /etc/logrotate.conf
chown root:root /etc/logrotate.conf
```

再次手动执行，日志已如预期进行了相应切割归档和压缩。

