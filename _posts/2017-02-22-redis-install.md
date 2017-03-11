---
layout: post
title:  "Redis安装记录"
date:   2017-02-22 14:06:05
categories: 安装设置
tags: Redis 缓存
excerpt: 记下Redis下载编译安装和启动的过程。
---

# redis安装
## 准备工作
创建一个redis用户用于运行redis

```
useradd -g usr -s /bin/bash -d /usr/users/redis -m redis
```

确保依赖：

```
sudo yum install make
sudo yum install gcc
sudo yum install -y tcl
```

创建源码保存位置
```
mkdir src
cd src
```

下载Redis最新稳定版源码包：

```
[redis@test src]$ wget http://download.redis.io/redis-stable.tar.gz
--2017-02-21 00:15:41--  http://download.redis.io/redis-stable.tar.gz
正在解析主机 download.redis.io (download.redis.io)... 109.74.203.151
正在连接 download.redis.io (download.redis.io)|109.74.203.151|:80... 已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度：1575076 (1.5M) [application/x-gzip]
正在保存至: “redis-stable.tar.gz”
100%[========================================================================================>] 1,575,076   14.0KB/s 用时 2m 26s 

2017-02-21 00:18:07 (10.6 KB/s) - 已保存 “redis-stable.tar.gz” [1575076/1575076])
```

## 开始安装

解压缩源码包：

```
[redis@test src]$ ll
总用量 1540
-rw-r--r--. 1 redis usr 1575076 2月  12 23:15 redis-stable.tar.gz
[redis@test src]$ tar xzvf redis-stable.tar.gz 
.......
.......
[redis@test src]$ ll
总用量 1544
drwxr-xr-x. 6 redis usr    4096 2月  12 23:14 redis-stable
-rw-r--r--. 1 redis usr 1575076 2月  12 23:15 redis-stable.tar.gz
```

编译：

```
[redis@test src]$ cd redis-stable/
[redis@test src]# make
[redis@test src]# sudo make install
```


安装完成，可在/usr/local/bin下找到新生生成的Redis相关命令

 	 [root@test redis-stable]# ll /usr/local/bin | grep redis
 	 -rwxr-xr-x. 1 root root 2431912 2月  21 00:21 redis-benchmark
     -rwxr-xr-x. 1 root root   25184 2月  21 00:21 redis-check-aof
     -rwxr-xr-x. 1 root root 5181800 2月  21 00:21 redis-check-rdb
     -rwxr-xr-x. 1 root root 2584776 2月  21 00:21 redis-cli
     lrwxrwxrwx. 1 root root      12 2月  21 00:21 redis-sentinel -> redis-server
     -rwxr-xr-x. 1 root root 5181800 2月  21 00:21 redis-server

这几个可执行文件的作用： 

```
redis-benchmark：性能测试工具，测试Redis在你的系统及配置下的读写性能 
redis-check-aof：用于修复出问题的AOF文件 
redis-check-dump：用于修复出问题的dump.rdb文件 
redis-cli：Redis命令行交互客户端
redis-sentinel：Redis集群的管理工具 
redis-server：Redis服务器启动程序 
```

## 启动Redis

```
[redis@test redis-stable]$ redis-server
4004:C 21 Feb 23:37:18.305 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
4004:M 21 Feb 23:37:18.306 # You requested maxclients of 10000 requiring at least 10032 max file descriptors.
4004:M 21 Feb 23:37:18.306 # Server can't set maximum open files to 10032 because of OS error: Operation not permitted.
4004:M 21 Feb 23:37:18.306 # Current maximum open files is 4096. maxclients has been reduced to 4064 to compensate for low ulimit. If you need higher maxclients increase 'ulimit -n'.
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 3.2.8 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 4004
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

4004:M 21 Feb 23:37:18.335 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
4004:M 21 Feb 23:37:18.335 # Server started, Redis version 3.2.8
4004:M 21 Feb 23:37:18.335 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
4004:M 21 Feb 23:37:18.335 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
4004:M 21 Feb 23:37:18.336 * DB loaded from disk: 0.000 seconds
4004:M 21 Feb 23:37:18.336 * The server is now ready to accept connections on port 6379
```
---
## 阅读启动提示

启动时几个WARNING和提示：

1. Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf \
没有制定配置文件，使用默认的配置。使用"redis-server /path/to/redis" 方式指定加载一个配置文件方式启动。

2. You requested maxclients of 10000 requiring at least 10032 max file descriptors. \
你指定的最大连接数10000要求至少最大文件描述符要10032个。

3. Server can't set maximum open files to 10032 because of OS error: Operation not permitted. \
因为一个操作系统错误服务器不能设置最大的打开文件数为10032：没有操作权限。

4. Current maximum open files is 4096. maxclients has been reduced to 4064 to compensate for low ulimit. If you need higher maxclients increase 'ulimit -n'. \
当前最大打开文件数为4096。作为低限制关系的补偿，最大连接数被减少为4064。如果你需要更高的最大连接数，增加'ulimit -n'的配置。

5. WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128. \
TCP的储配未交付设置值511不能被强制生效，因为 /proc/sys/net/core/somaxconn 设置为一个较低的值128。

6. WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect. \
overcommit_memory 设置为0！ 在低内存环境下的后台保存可能失败。在文件 /etc/sysctl.conf  中添加'vm.overcommit_memory = 1'，然后重启或运行命令'sysctl vm.overcommit_memory=1'使设置生效，来修复这个问题。

7. WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled. \
你的"Transparent Huge Pages (THP)"支持在内核中启用了。 这将在使用Redis时造成延迟和内存使用问题。使用root运行命令'echo never > /sys/kernel/mm/transparent_hugepage/enabled'，并且将它添加到你的/etc/rc.local中使在重启后仍然保持生效来修复这个问题。在THP禁用之后Redis必须重启。

## Hello Redis

打开另一个终端：

```
[root@test home]# redis-cli 
127.0.0.1:6379> 
127.0.0.1:6379> 
127.0.0.1:6379> help
redis-cli 3.2.8
To get help about Redis commands type:
      "help @<group>" to get a list of commands in <group>
      "help <command>" for help on <command>
      "help <tab>" to get a list of possible help topics
      "quit" to exit

To set redis-cli perferences:
      ":set hints" enable online hints
      ":set nohints" disable online hints
Set your preferences in ~/.redisclirc
127.0.0.1:6379> 
127.0.0.1:6379> set firstkey "Hello Redis! This is the first key!"
OK
127.0.0.1:6379> 
127.0.0.1:6379> 
127.0.0.1:6379> get firstkey
"Hello Redis! This is the first key!"
127.0.0.1:6379> 
```


    

 