---
layout: post
title:  "CentOS7 安装netstat命令"
date:   2020-06-25 14:07:34
categories: 
tags:  CentOS
excerpt: 
---

在CentOS7最小安装没有netstat命令，需要手动安装net-tools包。


```
[sw@test html]$ netstat -lnp
-bash: netstat: command not found
```

安装net-tools：

```
yum install net-tools
```

### net-tools包基本信息

```
[root@test docker_rpm]# yum info net-tools
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Installed Packages
Name        : net-tools
Arch        : x86_64
Version     : 2.0
Release     : 0.25.20131004git.el7
Size        : 917 k
Repo        : installed
From repo   : base
Summary     : Basic networking tools
URL         : http://sourceforge.net/projects/net-tools/
License     : GPLv2+
Description : The net-tools package contains basic networking tools,
            : including ifconfig, netstat, route, and others.
            : Most of them are obsolete. For replacement check iproute package.
```

### net-tools包中包含的命令

```
[root@test docker_rpm]# repoquery -ql net-tools
/bin/netstat
/sbin/arp
/sbin/ether-wake
/sbin/ifconfig
/sbin/ipmaddr
/sbin/iptunnel
/sbin/mii-diag
/sbin/mii-tool
/sbin/nameif
/sbin/plipconfig
/sbin/route
/sbin/slattach
```

