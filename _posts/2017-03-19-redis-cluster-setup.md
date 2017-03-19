---
layout: post
title:  "Redis 3.2.8集群部署实验"
date:   2017-03-19 23:30:05
categories: 
tags:  Redis 缓存 Cluster
excerpt: 1副本集群的安装设置过程记录。
---

最近工作中学到的redis集群部署方法，回家照着工作操作记录，又做了点实验验证一些安装步骤的事情。

实验中用两台服务器，各部署三个redis实例，来实现1副本的redis集群。

操作系统为： CentOS Linux release 7.3.1611 (Core)

| No.| hostname | IP address | redis port |
| --- | ---| --- | --- | 
| 1 | test-140 | 11.11.11.140| 10010 |
| 2 | test-140 | 11.11.11.140| 10020 |
| 3 | test-140 | 11.11.11.140| 10030 |
| 4 | test-141 | 11.11.11.141| 10010 |
| 5 | test-141 | 11.11.11.141| 10020 |
| 6 | test-141 | 11.11.11.141| 10030 |

### 1. 创建部署用户

```
[root@test-140 ~]# useradd -d /usr/users/redis -g usr -m redis
[root@test-140 ~]# passwd redis
更改用户 redis 的密码 。
新的 密码：
无效的密码： 密码少于 8 个字符
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。
[root@test-140 ~]# 

```

test-141上进行相同操作。

### 2. 获取源码包

从[官方网站](https://redis.io)下载源码包，当前是3.2.8。或直接在服务器中wget

```
[root@test-140 ~]# su - redis
[redis@test-140 ~]$ wget http://download.redis.io/releases/redis-3.2.8.tar.gz
--2017-03-19 18:55:50--  http://download.redis.io/releases/redis-3.2.8.tar.gz
正在解析主机 download.redis.io (download.redis.io)... 109.74.203.151
正在连接 download.redis.io (download.redis.io)|109.74.203.151|:80... 已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度：1547237 (1.5M) [application/x-gzip]
正在保存至: “redis-3.2.8.tar.gz”

100%[=============================================>] 1,547,237   7.47KB/s 用时 1m 59s 

2017-03-19 18:57:50 (12.7 KB/s) - 已保存 “redis-3.2.8.tar.gz” [1547237/1547237])
```

解压缩：

```
[redis@test-140 ~]$ tar xzf redis-3.2.8.tar.gz 
[redis@test-140 ~]$ ll
总用量 1516
drwxr-xr-x. 6 redis usr    4096 2月  12 23:14 redis-3.2.8
-rw-r--r--. 1 redis usr 1547237 2月  12 23:15 redis-3.2.8.tar.gz
```

### 3. 编译安装

make编译源码

```
[redis@test-140 ~]$ cd redis-3.2.8/
[redis@test-140 redis-3.2.8]$ make
    .......
    .......
    (省略若干行，没有报错即可）
    .......
    LINK redis-cli
    CC redis-benchmark.o
    LINK redis-benchmark
    INSTALL redis-check-rdb
    CC redis-check-aof.o
    LINK redis-check-aof

Hint: It's a good idea to run 'make test' ;)

make[1]: 离开目录“/usr/users/redis/redis-3.2.8/src”
[redis@test-140 redis-3.2.8]$ 
```

make install安装程序，并使用 **PREFIX** 指定安装位置

```
[redis@test-140 redis-3.2.8]$ make PREFIX=/usr/users/redis/app/redis install
cd src && make install
make[1]: 进入目录“/usr/users/redis/redis-3.2.8/src”

Hint: It's a good idea to run 'make test' ;)

    INSTALL install
    INSTALL install
    INSTALL install
    INSTALL install
    INSTALL install
make[1]: 离开目录“/usr/users/redis/redis-3.2.8/src”
```

redis已经被安装到 /usr/users/redis/app/redis/ 之下：

```
[redis@test-140 redis-3.2.8]$ cd ../app/redis/
[redis@test-140 redis]$ ll
总用量 0
drwxr-xr-x. 2 redis usr 134 3月  19 19:08 bin
```

可见里面现在只有一个bin目录：

```
[redis@test-140 redis]$ cd bin
[redis@test-140 bin]$ ll
总用量 15060
-rwxr-xr-x. 1 redis usr 2431904 3月  19 19:08 redis-benchmark
-rwxr-xr-x. 1 redis usr   25176 3月  19 19:08 redis-check-aof
-rwxr-xr-x. 1 redis usr 5181792 3月  19 19:08 redis-check-rdb
-rwxr-xr-x. 1 redis usr 2584768 3月  19 19:08 redis-cli
lrwxrwxrwx. 1 redis usr      12 3月  19 19:08 redis-sentinel -> redis-server
-rwxr-xr-x. 1 redis usr 5181792 3月  19 19:08 redis-server
```

redis所有的程序都在里面了。可以根据需要决定是否将这个目录加入到系统环境变量的PATH中。

> 中途吃个饭休息休息。。。

### 4. 配置文件准备

在源码目录中，将配置文件模版redis.conf复制到redis配置文件目录中：

```
[redis@test-140 bin]$ cd ~/redis-3.2.8/
[redis@test-140 redis-3.2.8]$ mkdir ~/app/redis/conf
[redis@test-140 redis-3.2.8]$ cp redis.conf ~/app/redis/conf/
[redis@test-140 redis-3.2.8]$ cd ~/app/redis/conf/
[redis@test-140 conf]$ ll
总用量 48
-rw-r--r--. 1 redis usr 46695 3月  19 21:56 redis.conf
```

将配置文件模版复制一份，命名为 redis10010.conf，并编辑文集，修改配置：

```
[redis@test-140 conf]$ cp redis.conf redis10010.conf
[redis@test-140 conf]$ vi redis10010.conf
```

#### 4.1. 修改配置文件

1. 61行，设置bind地址 0.0.0.0

   ```
   bind 0.0.0.0
   ```
   
1. 84行，修改服务端口

   ```
   port 10010
   ```
   
1. 128行，修改daemonize为yes，即以后台程序方式运行

    ```
    daemonize yes   
    ```
    
1. 150行，修改pid生成路径

   ```
   pidfile "/usr/users/redis/app/redis/run/redis10010.pid"
   ```
   
   要手动创建run目录存放pid路径：
   
   ```
   mkdir /usr/users/redis/app/redis/run
   ```
   
1. 163行，修改日志文件的位置

   ```
   logfile "/usr/users/redis/app/redis/logs/redis10010/redis.log"
   ```
   
   要手动创建日志目录：
   
   ```
   mkdir -p /usr/users/redis/app/redis/logs/redis10010
   ```
   
1. 237行，数据文件名

   ```
   dbfilename "dump10010.rdb"
   ```
   

1. 247行，修改工作目录，数据文件、append only文件等会在该目录中生成。

   ```
   dir "/usr/users/redis/app/redis/wkd10010"
   ```
   
   需要手动创建该目录：
   
   ```
   mkdir /usr/users/redis/app/redis/wkd10010
   ```
   
   
1. 593行，是否每次更新操作进行日志记录:

   ```
   appendonly yes
   ```
   597行，文件名设置
   
   ```
   appendfilename "appendonly10010.aof"
   ```
   
1. 721行，是否支持集群

   ```
   cluster-enabled yes
   ```
   
1. 729行，集群配置文件名

   ```
   cluster-config-file "nodes-10010.conf"
   ```
   
   这个文件也会在工作目录产生。

#### 4.2. 为剩下两个节点复制修改配置文件

因一个机器上还要启动两个redis实例，分别用10020和10030端口，复制配置文件：

```
[redis@test-140 conf]$ cp redis10010.conf redis10020.conf
[redis@test-140 conf]$ 
[redis@test-140 conf]$ cp redis10010.conf redis10030.conf
[redis@test-140 conf]$ 
[redis@test-140 conf]$ ll
总用量 192
-rw-r--r--. 1 redis usr 46812 3月  19 22:18 redis10010.conf
-rw-r--r--. 1 redis usr 46812 3月  19 22:18 redis10020.conf
-rw-r--r--. 1 redis usr 46812 3月  19 22:18 redis10030.conf
-rw-r--r--. 1 redis usr 46695 3月  19 21:56 redis.conf
[redis@test-140 conf]$ 
```

统一替换配置文件 **redis10020.conf** 和 **redis10030.conf** 中的10010为10020和10030:

```
[redis@test-140 conf]$ sed -i 's/10010/10020/' redis10020.conf
[redis@test-140 conf]$ sed -i 's/10010/10030/' redis10030.conf
```   

要手动创建10020和10030的日志目录和工作目录：

```
[redis@test-140 conf]$ mkdir -p /usr/users/redis/app/redis/logs/redis10020
[redis@test-140 conf]$ mkdir /usr/users/redis/app/redis/wkd10020
[redis@test-140 conf]$ mkdir -p /usr/users/redis/app/redis/logs/redis10030
[redis@test-140 conf]$ mkdir /usr/users/redis/app/redis/wkd10030
```

#### 4.3. 将redis复制到test-141服务器的相同路径下

当然，不嫌麻烦在test-141上重复以上操作也可以。  

```
[redis@test-140 ~]$ scp -r app/ redis@11.11.11.141:/usr/users/redis/
The authenticity of host '11.11.11.141 (11.11.11.141)' can't be established.
ECDSA key fingerprint is e0:a3:10:01:7b:2c:1a:15:3d:c3:4a:66:7a:eb:3d:9c.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '11.11.11.141' (ECDSA) to the list of known hosts.
redis@11.11.11.141's password: 
redis-server                                      100% 5060KB   4.9MB/s   00:00    
redis-benchmark                                   100% 2375KB   2.3MB/s   00:00    
redis-cli                                         100% 2524KB   2.5MB/s   00:01    
redis-check-rdb                                   100% 5060KB   4.9MB/s   00:00    
redis-check-aof                                   100%   25KB  24.6KB/s   00:00    
redis-sentinel                                    100% 5060KB   4.9MB/s   00:00    
redis.conf                                        100%   46KB  45.6KB/s   00:00    
redis10010.conf                                   100%   46KB  45.7KB/s   00:00    
redis10020.conf                                   100%   46KB  45.7KB/s   00:00    
redis10030.conf                                   100%   46KB  45.7KB/s   00:00    
[redis@test-140 ~]$ 
```

### 5. 启动redis服务

分别在test-140和test-141上启动三个redis实例：

#### test-140：

```
[redis@test-140 ~]$ cd ~/app/redis/bin
[redis@test-140 bin]$ ./redis-server /usr/users/redis/app/redis/conf/redis10010.conf
[redis@test-140 bin]$ ./redis-server /usr/users/redis/app/redis/conf/redis10020.conf
[redis@test-140 bin]$ ./redis-server /usr/users/redis/app/redis/conf/redis10030.conf
[redis@test-140 bin]$ 
[redis@test-140 bin]$ 
[redis@test-140 bin]$ ps -ef |grep redis
root      3688  2925  0 18:50 pts/0    00:00:00 su - redis
redis     3689  3688  0 18:50 pts/0    00:00:00 -bash
root      9165  9129  0 22:02 pts/1    00:00:00 su - redis
redis     9166  9165  0 22:02 pts/1    00:00:00 -bash
redis     9519     1  0 22:29 ?        00:00:00 ./redis-server 0.0.0.0:10010 [cluster]
redis     9523     1  0 22:29 ?        00:00:00 ./redis-server 0.0.0.0:10020 [cluster]
redis     9527     1  0 22:29 ?        00:00:00 ./redis-server 0.0.0.0:10030 [cluster]
redis     9538  3689  0 22:29 pts/0    00:00:00 ps -ef
redis     9539  3689  0 22:29 pts/0    00:00:00 grep --color=auto redis
[redis@test-140 bin]$ 

```

#### test-141：

```
[redis@test-141 ~]$ cd ~/app/redis/bin
[redis@test-141 bin]$ ll
总用量 20124
-rwxr-xr-x. 1 redis usr 2431904 3月  19 22:25 redis-benchmark
-rwxr-xr-x. 1 redis usr   25176 3月  19 22:25 redis-check-aof
-rwxr-xr-x. 1 redis usr 5181792 3月  19 22:25 redis-check-rdb
-rwxr-xr-x. 1 redis usr 2584768 3月  19 22:25 redis-cli
-rwxr-xr-x. 1 redis usr 5181792 3月  19 22:25 redis-sentinel
-rwxr-xr-x. 1 redis usr 5181792 3月  19 22:25 redis-server
[redis@test-141 bin]$ ./redis-server /usr/users/redis/app/redis/conf/redis10010.conf
[redis@test-141 bin]$ ./redis-server /usr/users/redis/app/redis/conf/redis10020.conf
[redis@test-141 bin]$ ./redis-server /usr/users/redis/app/redis/conf/redis10030.conf
[redis@test-141 bin]$ ps -ef |grep redis
root      5740  3068  0 22:25 pts/0    00:00:00 su - redis
redis     5741  5740  0 22:25 pts/0    00:00:00 -bash
redis     5889     1  0 22:30 ?        00:00:00 ./redis-server 0.0.0.0:10010 [cluster]
redis     5893     1  0 22:31 ?        00:00:00 ./redis-server 0.0.0.0:10020 [cluster]
redis     5897     1  0 22:31 ?        00:00:00 ./redis-server 0.0.0.0:10030 [cluster]
redis     5900  5741  0 22:31 pts/0    00:00:00 ps -ef
redis     5901  5741  0 22:31 pts/0    00:00:00 grep --color=auto redis
[redis@test-141 bin]$ 
```

### 6. 使用redis-trib.rb组建集群

redis的源码里，提供了一个ruby脚本：**redis-trib.rb**，使用该脚本来进行一些集群操作，但首先要确保安装了ruby依赖，并安装redis的gem模块。

#### 6.1. 安装依赖：

```
yum -y install ruby-devel ruby-irb ruby-libs ruby-rdoc ruby rubygems-devel rubygems
```
以上包在CentOS7的安装DVD镜像中有。

安装redis gem：

```
[root@test-140 ~]# gem install redis
Fetching: redis-3.3.3.gem (100%)
Successfully installed redis-3.3.3
Parsing documentation for redis-3.3.3
Installing ri documentation for redis-3.3.3
1 gem installed
```

如是内网环境的服务器，可以事先访问rubygems： [redis gem](https://rubygems.org/gems/redis) 右下角有下载链接，将redis-3.3.3.gem包下载到本地，然后倒腾进系统里安装。

直接下载链接：[https://rubygems.org/downloads/redis-3.3.3.gem](https://rubygems.org/downloads/redis-3.3.3.gem)

安装命令：

```
[root@test-141 ~]# wget https://rubygems.org/downloads/redis-3.3.3.gem
--2017-03-19 22:50:53--  https://rubygems.org/downloads/redis-3.3.3.gem
正在解析主机 rubygems.org (rubygems.org)... 151.101.64.70, 151.101.0.70, 151.101.128.70, ...
正在连接 rubygems.org (rubygems.org)|151.101.64.70|:443... 已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度：92672 (90K) [application/octet-stream]
正在保存至: “redis-3.3.3.gem”

100%[===============================================>] 92,672       310KB/s 用时 0.3s   

2017-03-19 22:50:53 (310 KB/s) - 已保存 “redis-3.3.3.gem” [92672/92672])

[root@test-141 ~]# ll
总用量 100
-rw-------. 1 root root  1761 2月  18 00:25 anaconda-ks.cfg
-rw-r--r--. 1 root root  1809 2月  18 00:39 initial-setup-ks.cfg
-rw-r--r--. 1 root root 92672 1月  24 01:21 redis-3.3.3.gem
[root@test-141 ~]# 
[root@test-141 ~]# 
[root@test-141 ~]# gem install --local redis-3.3.3.gem
Successfully installed redis-3.3.3
Parsing documentation for redis-3.3.3
Installing ri documentation for redis-3.3.3
1 gem installed
```

#### 6.2. 集群创建：

先来看看redis-trib.rb脚本的信息：

```
[redis@test-140 bin]$ cd ~/redis-3.2.8/src/
[redis@test-140 src]$ ./redis-trib.rb 
Usage: redis-trib <command> <options> <arguments ...>

  create          host1:port1 ... hostN:portN
                  --replicas <arg>
  check           host:port
  info            host:port
  fix             host:port
                  --timeout <arg>
  reshard         host:port
                  --from <arg>
                  --to <arg>
                  --slots <arg>
                  --yes
                  --timeout <arg>
                  --pipeline <arg>
  rebalance       host:port
                  --weight <arg>
                  --auto-weights
                  --use-empty-masters
                  --timeout <arg>
                  --simulate
                  --pipeline <arg>
                  --threshold <arg>
  add-node        new_host:new_port existing_host:existing_port
                  --slave
                  --master-id <arg>
  del-node        host:port node_id
  set-timeout     host:port milliseconds
  call            host:port command arg arg .. arg
  import          host:port
                  --from <arg>
                  --copy
                  --replace
  help            (show this help)

For check, fix, reshard, del-node, set-timeout you can specify the host and port of any working node in the cluster.

```

创建集群： redis-trib.rb create ，参数 --replicas 是副本数量。

我们要创建1副本的集群，所以命令这样写：

```
[redis@test-140 src]$ ./redis-trib.rb create --replicas 1 11.11.11.140:10010 11.11.11.140:10020 11.11.11.140:10030 11.11.11.141:10010 11.11.11.141:10020 11.11.11.141:10030
>>> Creating cluster
[ERR] Sorry, can't connect to node 11.11.11.141:10010
```

结果提示141节点的端口无法访问到。

**忘记了重要的一步！！停止防火墙！！！停止防火墙！！！！停止防火墙！！！！！**

```
[root@test-140 ~]# systemctl stop firewalld.service 
[root@test-140 ~]# systemctl disable firewalld.service 
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
Removed symlink /etc/systemd/system/basic.target.wants/firewalld.service.
```

```
[root@test-141 ~]# systemctl stop firewalld.service 
[root@test-141 ~]# systemctl disable firewalld.service 
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
Removed symlink /etc/systemd/system/basic.target.wants/firewalld.service.
```

再次执行：

```
[redis@test-140 src]$ ./redis-trib.rb create --replicas 1 11.11.11.140:10010 11.11.11.140:10020 11.11.11.140:10030 11.11.11.141:10010 11.11.11.141:10020 11.11.11.141:10030
>>> Creating cluster
>>> Performing hash slots allocation on 6 nodes...
Using 3 masters:
11.11.11.140:10010
11.11.11.141:10010
11.11.11.140:10020
Adding replica 11.11.11.141:10020 to 11.11.11.140:10010
Adding replica 11.11.11.140:10030 to 11.11.11.141:10010
Adding replica 11.11.11.141:10030 to 11.11.11.140:10020
M: adce8b353bfa91e27e1ff280b6f2d20024e67646 11.11.11.140:10010
   slots:0-5460 (5461 slots) master
M: 301baceeff8ab065e1122761a9012bf4fcbabebd 11.11.11.140:10020
   slots:10923-16383 (5461 slots) master
S: 398373d3f41d333bc5bdfa97e55a58e821269dc8 11.11.11.140:10030
   replicates 249e478efb782feea4f4a5f3c97c957861e62016
M: 249e478efb782feea4f4a5f3c97c957861e62016 11.11.11.141:10010
   slots:5461-10922 (5462 slots) master
S: 3335ad6a1ab95932a15ca1dfa002384b4157ce2a 11.11.11.141:10020
   replicates adce8b353bfa91e27e1ff280b6f2d20024e67646
S: 89fe8cae9bc8a9f5e842001dd14cbc6f869d9481 11.11.11.141:10030
   replicates 301baceeff8ab065e1122761a9012bf4fcbabebd
Can I set the above configuration? (type 'yes' to accept): 
```

脚本将使用这6个节点的redis实例，构建1副本的集群。并且自己根据计算，自动指定了哪个节点是哪个节点的副本。

此时让用户确认该集群方案，貌似除了输入**yes**之外，我们也不能拒绝。

```
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join.....
>>> Performing Cluster Check (using node 11.11.11.140:10010)
M: adce8b353bfa91e27e1ff280b6f2d20024e67646 11.11.11.140:10010
   slots:0-5460 (5461 slots) master
   1 additional replica(s)
M: 249e478efb782feea4f4a5f3c97c957861e62016 11.11.11.141:10010
   slots:5461-10922 (5462 slots) master
   1 additional replica(s)
S: 398373d3f41d333bc5bdfa97e55a58e821269dc8 11.11.11.140:10030
   slots: (0 slots) slave
   replicates 249e478efb782feea4f4a5f3c97c957861e62016
S: 3335ad6a1ab95932a15ca1dfa002384b4157ce2a 11.11.11.141:10020
   slots: (0 slots) slave
   replicates adce8b353bfa91e27e1ff280b6f2d20024e67646
M: 301baceeff8ab065e1122761a9012bf4fcbabebd 11.11.11.140:10020
   slots:10923-16383 (5461 slots) master
   1 additional replica(s)
S: 89fe8cae9bc8a9f5e842001dd14cbc6f869d9481 11.11.11.141:10030
   slots: (0 slots) slave
   replicates 301baceeff8ab065e1122761a9012bf4fcbabebd
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
[redis@test-140 src]$ 
```

输入**yes**之后，开始创建集群，三个Master，三个Slave，以及它们的对应关系从此确定。集群共分配了**16384**个哈希槽。

redis-trib.rb脚本默认都是将集群分为16384个哈希槽，在redis-trib.rb脚本中的第27行，有变量```ClusterHashSlots = 16384```，应该就是这里设置。之前忘记改了，回头有时间再多实验几次。

>集群创建之后，每个节点的工作目录中都会生成一个集群配置文件，里面记录有创建集群时的集群配置信息。

### 7. 一些验证：

创建完集群，可以做一些简单的验证：


#### 使用 redis-cli 控制台：

```
[redis@test-141 ~]$ cd app/redis/bin/
[redis@test-141 bin]$ ./redis-cli -c -h 11.11.11.140 -p 10020
11.11.11.140:10020> set testkey "Hello Redis Cluster"
-> Redirected to slot [4757] located at 11.11.11.140:10010
OK
11.11.11.140:10010> keys *
1) "testkey"
11.11.11.140:10010> 

```

在141机器上，连接到140的10020节点，写入一个key，被分配到4757槽，该槽位在140的10010节点，所以被重定向到了140的10010节点。

我们连接到一个Slave节点，再读出该key：

```
[redis@test-141 bin]$ ./redis-cli -c -h 11.11.11.141 -p 10030
11.11.11.141:10030> get testkey
-> Redirected to slot [4757] located at 11.11.11.140:10010
"Hello Redis Cluster"
11.11.11.140:10010> 
```

被重定向到4757槽所在的140的10010节点，拿出了该key的值。

#### 使用 redis-trib.rb  check 任意节点：

```
[redis@test-140 src]$ ./redis-trib.rb  check 11.11.11.141:10020
>>> Performing Cluster Check (using node 11.11.11.141:10020)
S: 3335ad6a1ab95932a15ca1dfa002384b4157ce2a 11.11.11.141:10020
   slots: (0 slots) slave
   replicates adce8b353bfa91e27e1ff280b6f2d20024e67646
M: adce8b353bfa91e27e1ff280b6f2d20024e67646 11.11.11.140:10010
   slots:0-5460 (5461 slots) master
   1 additional replica(s)
M: 301baceeff8ab065e1122761a9012bf4fcbabebd 11.11.11.140:10020
   slots:10923-16383 (5461 slots) master
   1 additional replica(s)
M: 249e478efb782feea4f4a5f3c97c957861e62016 11.11.11.141:10010
   slots:5461-10922 (5462 slots) master
   1 additional replica(s)
S: 89fe8cae9bc8a9f5e842001dd14cbc6f869d9481 11.11.11.141:10030
   slots: (0 slots) slave
   replicates 301baceeff8ab065e1122761a9012bf4fcbabebd
S: 398373d3f41d333bc5bdfa97e55a58e821269dc8 11.11.11.140:10030
   slots: (0 slots) slave
   replicates 249e478efb782feea4f4a5f3c97c957861e62016
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
[redis@test-140 src]$ 
```

check任意节点可看当前集群状态。





