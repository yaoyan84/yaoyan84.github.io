---
layout: post
title:  "Linux dns服务 bind安装配置"
date:   2017-06-13 23:06:05
categories: Linux
tags: bind dns服务
excerpt: Linux dns服务 bind安装配置。
---

### 环境：

CentOS7.3，IP地址： 192.168.0.101


### 1. 执行安装： yum install bind

```
[root@SWhgfirstbalance2 ~]# yum install bind
已加载插件：fastestmirror, langpacks
Loading mirror speeds from cached hostfile
正在解决依赖关系
--> 正在检查事务
---> 软件包 bind.x86_64.32.9.9.4-38.el7_3.3 将被 安装
--> 正在处理依赖关系 bind-libs = 32:9.9.4-38.el7_3.3，它被软件包 32:bind-9.9.4-38.el7_3.3.x86_64 需要
--> 正在处理依赖关系 libGeoIP.so.1()(64bit)，它被软件包 32:bind-9.9.4-38.el7_3.3.x86_64 需要
--> 正在检查事务
---> 软件包 GeoIP.x86_64.0.1.5.0-11.el7 将被 安装
---> 软件包 bind-libs.x86_64.32.9.9.4-29.el7 将被 升级
--> 正在处理依赖关系 bind-libs = 32:9.9.4-29.el7，它被软件包 32:bind-utils-9.9.4-29.el7.x86_64 需要
---> 软件包 bind-libs.x86_64.32.9.9.4-38.el7_3.3 将被 更新
--> 正在处理依赖关系 bind-license = 32:9.9.4-38.el7_3.3，它被软件包 32:bind-libs-9.9.4-38.el7_3.3.x86_64 需要
--> 正在检查事务
---> 软件包 bind-license.noarch.32.9.9.4-29.el7 将被 升级
--> 正在处理依赖关系 bind-license = 32:9.9.4-29.el7，它被软件包 32:bind-libs-lite-9.9.4-29.el7.x86_64 需要
---> 软件包 bind-license.noarch.32.9.9.4-38.el7_3.3 将被 更新
---> 软件包 bind-utils.x86_64.32.9.9.4-29.el7 将被 升级
---> 软件包 bind-utils.x86_64.32.9.9.4-38.el7_3.3 将被 更新
--> 正在检查事务
---> 软件包 bind-libs-lite.x86_64.32.9.9.4-29.el7 将被 升级
---> 软件包 bind-libs-lite.x86_64.32.9.9.4-38.el7_3.3 将被 更新
--> 解决依赖关系完成

依赖关系解决

=================================================================================================================================================================================
 Package                                      架构                                 版本                                              源                                     大小
=================================================================================================================================================================================
正在安装:
 bind                                         x86_64                               32:9.9.4-38.el7_3.3                               updates                               1.8 M
为依赖而安装:
 GeoIP                                        x86_64                               1.5.0-11.el7                                      base                                  1.1 M
为依赖而更新:
 bind-libs                                    x86_64                               32:9.9.4-38.el7_3.3                               updates                               1.0 M
 bind-libs-lite                               x86_64                               32:9.9.4-38.el7_3.3                               updates                               730 k
 bind-license                                 noarch                               32:9.9.4-38.el7_3.3                               updates                                83 k
 bind-utils                                   x86_64                               32:9.9.4-38.el7_3.3                               updates                               202 k

事务概要
=================================================================================================================================================================================
安装  1 软件包 (+1 依赖软件包)
升级           ( 4 依赖软件包)

总下载量：4.8 M
Is this ok [y/d/N]: y
Downloading packages:
Delta RPMs reduced 1.7 M of updates to 592 k (66% saved)
(1/6): bind-libs-9.9.4-29.el7_9.9.4-38.el7_3.3.x86_64.drpm                                                                                                | 336 kB  00:00:04     
(2/6): bind-libs-lite-9.9.4-29.el7_9.9.4-38.el7_3.3.x86_64.drpm                                                                                           | 256 kB  00:00:04     
警告：/var/cache/yum/x86_64/7/updates/packages/bind-license-9.9.4-38.el7_3.3.noarch.rpm: 头V3 RSA/SHA256 Signature, 密钥 ID f4a80eb5: NOKEY    ] 442 kB/s | 1.2 MB  00:00:05 ETA
bind-license-9.9.4-38.el7_3.3.noarch.rpm 的公钥尚未安装
(3/6): bind-license-9.9.4-38.el7_3.3.noarch.rpm                                                                                                           |  83 kB  00:00:00     
GeoIP-1.5.0-11.el7.x86_64.rpm 的公钥尚未安装m                             50% [================================                                ] 546 kB/s | 1.9 MB  00:00:03 ETA
(4/6): GeoIP-1.5.0-11.el7.x86_64.rpm                                                                                                                      | 1.1 MB  00:00:04     
(5/6): bind-utils-9.9.4-38.el7_3.3.x86_64.rpm                                                                                                             | 202 kB  00:00:00     
(6/6): bind-9.9.4-38.el7_3.3.x86_64.rpm                                                                                                                   | 1.8 MB  00:00:05     
Finishing delta rebuilds of 2 package(s) (1.7 M)
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
总计                                                                                                                                             313 kB/s | 3.7 MB  00:00:12     
从 http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7 检索密钥
导入 GPG key 0xF4A80EB5:
 用户ID     : "CentOS-7 Key (CentOS 7 Official Signing Key) <security@centos.org>"
 指纹       : 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
 来自       : http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7
是否继续？[y/N]：y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : GeoIP-1.5.0-11.el7.x86_64                                                                                                                                   1/10
  正在更新    : 32:bind-license-9.9.4-38.el7_3.3.noarch                                                                                                                     2/10
  正在更新    : 32:bind-libs-9.9.4-38.el7_3.3.x86_64                                                                                                                        3/10
  正在安装    : 32:bind-9.9.4-38.el7_3.3.x86_64                                                                                                                             4/10
  正在更新    : 32:bind-utils-9.9.4-38.el7_3.3.x86_64                                                                                                                       5/10
  正在更新    : 32:bind-libs-lite-9.9.4-38.el7_3.3.x86_64                                                                                                                   6/10
  清理        : 32:bind-utils-9.9.4-29.el7.x86_64                                                                                                                           7/10
  清理        : 32:bind-libs-9.9.4-29.el7.x86_64                                                                                                                            8/10
  清理        : 32:bind-libs-lite-9.9.4-29.el7.x86_64                                                                                                                       9/10
  清理        : 32:bind-license-9.9.4-29.el7.noarch                                                                                                                        10/10
  验证中      : 32:bind-9.9.4-38.el7_3.3.x86_64                                                                                                                             1/10
  验证中      : 32:bind-libs-lite-9.9.4-38.el7_3.3.x86_64                                                                                                                   2/10
  验证中      : 32:bind-utils-9.9.4-38.el7_3.3.x86_64                                                                                                                       3/10
  验证中      : 32:bind-libs-9.9.4-38.el7_3.3.x86_64                                                                                                                        4/10
  验证中      : GeoIP-1.5.0-11.el7.x86_64                                                                                                                                   5/10
  验证中      : 32:bind-license-9.9.4-38.el7_3.3.noarch                                                                                                                     6/10
  验证中      : 32:bind-license-9.9.4-29.el7.noarch                                                                                                                         7/10
  验证中      : 32:bind-libs-lite-9.9.4-29.el7.x86_64                                                                                                                       8/10
  验证中      : 32:bind-utils-9.9.4-29.el7.x86_64                                                                                                                           9/10
  验证中      : 32:bind-libs-9.9.4-29.el7.x86_64                                                                                                                           10/10

已安装:
  bind.x86_64 32:9.9.4-38.el7_3.3                                                                                                                                               

作为依赖被安装:
  GeoIP.x86_64 0:1.5.0-11.el7                                                                                                                                                   

作为依赖被升级:
  bind-libs.x86_64 32:9.9.4-38.el7_3.3     bind-libs-lite.x86_64 32:9.9.4-38.el7_3.3     bind-license.noarch 32:9.9.4-38.el7_3.3     bind-utils.x86_64 32:9.9.4-38.el7_3.3   

完毕！
```



### 2. 修改配置文件 /etc/named.conf


```
vi /etc/named.conf
```

（1）

```
listen-on port 53 { 127.0.0.1; };
```

改为

```
listen-on port 53 { 192.168.0.101; };
```

（2）

```
allow-query     { localhost; };
```

改为

```
allow-query     { any; };
```


（3）增加域名配置：

```
// Customs setting:

zone "test.com" IN {
        type master;
        file "test.com.zone";
};


//end of custom settings
```

### 3. 在/var/named/增加配置文件test.com.zone

```
$TTL 600
@       IN       SOA    ns.test.com      admin.test.com. (
                        0       ; serial
                        1D      ; refresh
                        1H      ; retry
                        1W      ; expire
                        3H )    ; minimum
        IN      NS      ns
        IN      AAAA    ::1
ns      IN      A       192.168.0.101
app     IN      A       192.168.0.100
a 300   IN      CNAME   app
```

此配置文件，为自定义域名解析的真正配置文件。前几行是域名设置，包括版本，同步刷新时间等，后半部分为A类解析、CNAME解析等设置的详细信息，

分解如下：

```
@ IN SOA ns.test.com. admin.test.com
```

上面的IN表示后面的数据使用的是INTERNET标准。而@则代表相应的域名，如在这里代表test.com,即表示一个域名记录定义的开始。而 ns.test.com则是这个域的主域名服务器，而root.test.com则是管理员的邮件地址。注意这是邮件地址中用.来代替常见的邮件地址中的@。而SOA表示授权的开始。

```
0       ; serial
```

Serial 行前面的数字表示配置文件的修改版本，格式是年月日当日修改的修改的次数，每次修改这个配置文件时都应该修改这个数字，要不然你所作的修改不会更新到网上的其它DNS服务器的数据库上，即你所做的更新很可能对于不以你的所配置的DNS服务器为DNS服务器的客户端来说就不会反映出你的更新，也就对他们来说你更新是没有意义的。

```
1D      ; refresh
```

Refresh定义的是以为单位的刷新频率 即规定从域名服务器多长时间查询一个主服务器，以保证从服务器的数据是最新的。

```
1H      ; retry
```

Retry值规定了以秒为单位的重试的时间间隔，即当从服务试图在主服务器上查询更时，而连接失败了，则这个值规定了从服务多长时间后再试。

```
1W      ; expire
```

Expire这个用来规定从服务器在向主服务更新失败后多长时间后清除对应的记录，上述的数值是以分钟为单位的。

```
3H )    ; minimum
```

Minimum这个数据用来规定缓冲服务器不能与主服务联系上后多长时间清除相应的记录

因为我是在局域网搭设一个单独的DNS私服解析一些自定义域名，所以以上设置比较随意。

```
        IN      NS      ns
        IN      AAAA    ::1
```

指定本域的域名解析服务器。注意这两行前面必须有空格或者TAB。第一行是ipv4的，第二行是ipv6的。

```
ns IN A 192.168.0.101
app IN A 192.168.0.100
a 300 IN CNAME app
```

这就是DNS记录设置部分，平时修改和配置较多的地方。

 * ns 为主机名，A 代表地址类型为IPV4地址，192.168.0.101 是实际ip地址，这一条记录的含义是ns.test.com 的ip地址为 192.168.0.101；
 * app.test.com 地址解析为  192.168.0.100
 * a是一个CNAME别名，别名目标是app，即a.test.com是app.test.com的别名。


### 4. 启动服务

```
systemctl start named
```

不报错，即启动成功，查看状态

```
[root@SWhgfirstbalance2 named]# systemctl status named.service
● named.service - Berkeley Internet Name Domain (DNS)
   Loaded: loaded (/usr/lib/systemd/system/named.service; enabled; vendor preset: disabled)
   Active: active (running) since 二 2017-06-13 23:00:43 CST; 9min ago
  Process: 26328 ExecStop=/bin/sh -c /usr/sbin/rndc stop > /dev/null 2>&1 || /bin/kill -TERM $MAINPID (code=exited, status=0/SUCCESS)
  Process: 26354 ExecStart=/usr/sbin/named -u named $OPTIONS (code=exited, status=0/SUCCESS)
  Process: 26347 ExecStartPre=/bin/bash -c if [ ! "$DISABLE_ZONE_CHECKING" == "yes" ]; then /usr/sbin/named-checkconf -z /etc/named.conf; else echo "Checking of zone files is di
sabled"; fi (code=exited, status=0/SUCCESS) Main PID: 26358 (named)
   CGroup: /system.slice/named.service
           └─26358 /usr/sbin/named -u named

6月 13 23:06:01 SWhgfirstbalance2 named[26358]: error (network unreachable) resolving 'dns3.ourwebcdn.org/A/IN': 2001:500:e::1#53
6月 13 23:06:01 SWhgfirstbalance2 named[26358]: error (network unreachable) resolving 'dns1.ourwebcdn.org/A/IN': 2001:500:c::1#53
6月 13 23:06:01 SWhgfirstbalance2 named[26358]: error (network unreachable) resolving 'dns3.ourwebcdn.org/AAAA/IN': 2001:500:e::1#53
6月 13 23:06:01 SWhgfirstbalance2 named[26358]: error (network unreachable) resolving 'dns5.ourwebcdn.org/A/IN': 2001:500:c::1#53
6月 13 23:06:01 SWhgfirstbalance2 named[26358]: error (network unreachable) resolving 'dns3.ourwebcdn.org/A/IN': 2001:500:c::1#53
6月 13 23:06:01 SWhgfirstbalance2 named[26358]: error (network unreachable) resolving 'dns1.ourwebcdn.org/AAAA/IN': 2001:500:c::1#53
6月 13 23:06:01 SWhgfirstbalance2 named[26358]: error (network unreachable) resolving 'dns5.ourwebcdn.org/AAAA/IN': 2001:500:c::1#53
6月 13 23:06:01 SWhgfirstbalance2 named[26358]: error (network unreachable) resolving 'dns3.ourwebcdn.org/AAAA/IN': 2001:500:c::1#53
6月 13 23:06:02 SWhgfirstbalance2 named[26358]: error (network unreachable) resolving 's1.api.tv.itc.cn/A/IN': 2001:dc7::1#53
6月 13 23:06:02 SWhgfirstbalance2 named[26358]: error (network unreachable) resolving 'usr.mb.hd.sohu.com/A/IN': 2408:20f0:4010::20#53
```

设置为开机自启动：

```
systemctl enable named.service
```


### 5. 测试

（1）将本机windows的机器的dns设置为192.168.0.101

```
C:\Users\yycu>ping app.test.com

正在 Ping app.test.com [192.168.0.100] 具有 32 字节的数据:
来自 192.168.0.100 的回复: 字节=32 时间=1ms TTL=63
来自 192.168.0.100 的回复: 字节=32 时间=1ms TTL=63
来自 192.168.0.100 的回复: 字节=32 时间=2ms TTL=63
来自 192.168.0.100 的回复: 字节=32 时间=4ms TTL=63

192.168.0.100 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 1ms，最长 = 4ms，平均 = 2ms

C:\Users\yycu>ping a.test.com

正在 Ping app.test.com [192.168.0.100] 具有 32 字节的数据:
来自 192.168.0.100 的回复: 字节=32 时间=1ms TTL=63
来自 192.168.0.100 的回复: 字节=32 时间=2ms TTL=63
来自 192.168.0.100 的回复: 字节=32 时间=2ms TTL=63
来自 192.168.0.100 的回复: 字节=32 时间=2ms TTL=63

192.168.0.100 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 1ms，最长 = 2ms，平均 = 1ms

C:\Users\yycu>ping ns.test.com

正在 Ping ns.test.com [192.168.0.101] 具有 32 字节的数据:
来自 192.168.0.101 的回复: 字节=32 时间=1ms TTL=63
来自 192.168.0.101 的回复: 字节=32 时间=1ms TTL=63

192.168.0.101 的 Ping 统计信息:
    数据包: 已发送 = 2，已接收 = 2，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 1ms，最长 = 1ms，平均 = 1ms
Control-C
^C
C:\Users\yycu>ping baidu.com

正在 Ping baidu.com [220.181.57.217] 具有 32 字节的数据:
来自 220.181.57.217 的回复: 字节=32 时间=8ms TTL=50
来自 220.181.57.217 的回复: 字节=32 时间=9ms TTL=51
来自 220.181.57.217 的回复: 字节=32 时间=8ms TTL=50
来自 220.181.57.217 的回复: 字节=32 时间=10ms TTL=50

220.181.57.217 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 8ms，最长 = 10ms，平均 = 8ms
```

（2） 将一个虚机服务器的nameserver设置为192.168.0.101

```
[root@yaoyantest1 ~]# cat /etc/resolv.conf
# Generated by NetworkManager
nameserver 192.168.0.101
[root@yaoyantest1 ~]#
[root@yaoyantest1 ~]#
[root@yaoyantest1 ~]#
[root@yaoyantest1 ~]# ping app.test.com
PING app.test.com (192.168.0.100) 56(84) bytes of data.
64 bytes from 192.168.0.100 (192.168.0.100): icmp_seq=1 ttl=64 time=0.590 ms
64 bytes from 192.168.0.100 (192.168.0.100): icmp_seq=2 ttl=64 time=0.457 ms
64 bytes from 192.168.0.100 (192.168.0.100): icmp_seq=3 ttl=64 time=0.475 ms
64 bytes from 192.168.0.100 (192.168.0.100): icmp_seq=4 ttl=64 time=0.466 ms
64 bytes from 192.168.0.100 (192.168.0.100): icmp_seq=5 ttl=64 time=0.431 ms
64 bytes from 192.168.0.100 (192.168.0.100): icmp_seq=6 ttl=64 time=0.518 ms
^C
--- app.test.com ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5001ms
rtt min/avg/max/mdev = 0.431/0.489/0.590/0.056 ms
[root@yaoyantest1 ~]# ping www.baidu.com
PING www.a.shifen.com (111.13.100.92) 56(84) bytes of data.
64 bytes from 111.13.100.92 (111.13.100.92): icmp_seq=1 ttl=56 time=8.45 ms
64 bytes from 111.13.100.92 (111.13.100.92): icmp_seq=2 ttl=56 time=16.4 ms
64 bytes from 111.13.100.92 (111.13.100.92): icmp_seq=3 ttl=56 time=21.2 ms
^C
--- www.a.shifen.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2177ms
rtt min/avg/max/mdev = 8.458/15.394/21.280/5.287 ms
[root@yaoyantest1 ~]# ping www.baidu.com
```

可以发现，设置dns为自己搭建的192.168.0.101时，可以同时解析到自定义的域名和公网的域名。
