---
layout: post
title:  "在OracleLinux7上安装Oracle11gR2"
date:   2020-08-15 17:18:34
categories: 
tags:  Oracle
excerpt: 
---

###  系统安装

实验用虚拟机配置为 4CPU 8GB内存 200G磁盘。

安装时选择```Server with GUI```，并且勾选```Development Tools```和```System Administration Tools```。

![](/images/posts/2020/image-20200815103339951.png)

磁盘分区按照需要分配即可，实验采用系统自动分配。

### 环境初始化

将 ```oracle-rdbms-server-11gR2-preinstall``` 及相关的依赖包上次至服务器，开始相关环境初始化。

```bash
[root@testdb rpm]# ll
total 2488428
-rw-r--r--. 1 root root      17624 Aug 15 19:15 compat-libcap1-1.10-7.el7.x86_64.rpm
-rw-r--r--. 1 root root     194824 Aug 15 19:15 compat-libstdc++-33-3.2.3-72.el7.x86_64.rpm
-rw-r--r--. 1 root root      40144 Aug 15 19:15 elfutils-libelf-devel-0.176-4.el7.x86_64.rpm
-rw-r--r--. 1 root root     903336 Aug 15 19:15 ksh-20120801-142.0.1.el7.x86_64.rpm
-rw-r--r--. 1 root root      12624 Aug 15 19:15 libaio-devel-0.3.109-13.el7.x86_64.rpm
-rw-r--r--. 1 root root      22180 Aug 15 19:15 oracle-rdbms-server-11gR2-preinstall-1.0-6.el7.x86_64.rpm
-rw-r--r--. 1 root root 1395582860 Aug 15 19:16 p13390677_112040_Linux-x86-64_1of7.zip
-rw-r--r--. 1 root root 1151304589 Aug 15 19:16 p13390677_112040_Linux-x86-64_2of7.zip
-rw-r--r--. 1 root root      50580 Aug 15 19:15 zlib-devel-1.2.7-18.el7.x86_64.rpm
```

#### 安装 oracle-rdbms-server-11gR2-preinstall

```bash
yum install *.rpm
```

安装完成后，自动完成安装Oracle11gR2相关参数修改和用户创建等准备工作：

```bash
[root@testdb rpm]# id oracle
uid=54321(oracle) gid=54321(oinstall) groups=54321(oinstall),54322(dba)
[root@testdb rpm]# cat /etc/sysctl.conf
# sysctl settings are defined through files in
# /usr/lib/sysctl.d/, /run/sysctl.d/, and /etc/sysctl.d/.
#
# Vendors settings live in /usr/lib/sysctl.d/.
# To override a whole file, create a new file with the same in
# /etc/sysctl.d/ and put new settings there. To override
# only specific settings, add a file with a lexically later
# name in /etc/sysctl.d/ and put new settings there.
#
# For more information, see sysctl.conf(5) and sysctl.d(5).

# oracle-rdbms-server-11gR2-preinstall setting for fs.file-max is 6815744
fs.file-max = 6815744

# oracle-rdbms-server-11gR2-preinstall setting for kernel.sem is '250 32000 100 128'
kernel.sem = 250 32000 100 128

# oracle-rdbms-server-11gR2-preinstall setting for kernel.shmmni is 4096
kernel.shmmni = 4096

# oracle-rdbms-server-11gR2-preinstall setting for kernel.shmall is 2097152 on i386
kernel.shmall = 1073741824

# oracle-rdbms-server-11gR2-preinstall setting for kernel.shmmax is 4294967295 on i386
kernel.shmmax = 4398046511104

# oracle-rdbms-server-11gR2-preinstall setting for kernel.panic_on_oops is 1 per Orabug 19212317
kernel.panic_on_oops = 1

# oracle-rdbms-server-11gR2-preinstall setting for net.core.rmem_default is 262144
net.core.rmem_default = 262144

# oracle-rdbms-server-11gR2-preinstall setting for net.core.rmem_max is 4194304
net.core.rmem_max = 4194304

# oracle-rdbms-server-11gR2-preinstall setting for net.core.wmem_default is 262144
net.core.wmem_default = 262144

# oracle-rdbms-server-11gR2-preinstall setting for net.core.wmem_max is 1048576
net.core.wmem_max = 1048576

# oracle-rdbms-server-11gR2-preinstall setting for net.ipv4.conf.all.rp_filter is 2
net.ipv4.conf.all.rp_filter = 2

# oracle-rdbms-server-11gR2-preinstall setting for net.ipv4.conf.default.rp_filter is 2
net.ipv4.conf.default.rp_filter = 2

# oracle-rdbms-server-11gR2-preinstall setting for fs.aio-max-nr is 1048576
fs.aio-max-nr = 1048576

# oracle-rdbms-server-11gR2-preinstall setting for net.ipv4.ip_local_port_range is 9000 65500
net.ipv4.ip_local_port_range = 9000 65500
[root@testdb rpm]# cat /etc/security/limits.conf
# /etc/security/limits.conf
#
#This file sets the resource limits for the users logged in via PAM.
#It does not affect resource limits of the system services.
#Also note that configuration files in /etc/security/limits.d directory,
#which are read in alphabetical order, override the settings in this
#file in case the domain is the same or more specific.
#That means for example that setting a limit for wildcard domain here
#can be overriden with a wildcard setting in a config file in the
#subdirectory, but a user specific setting here can be overriden only
#with a user specific setting in the subdirectory.
#Each line describes a limit for a user in the form:
#<domain>        <type>  <item>  <value>
#Where:
#<domain> can be:
#        - a user name
#        - a group name, with @group syntax
#        - the wildcard *, for default entry
#        - the wildcard %, can be also used with %group syntax,
#                 for maxlogin limit
#<type> can have the two values:
#        - "soft" for enforcing the soft limits
#        - "hard" for enforcing hard limits
#<item> can be one of the following:
#        - core - limits the core file size (KB)
#        - data - max data size (KB)
#        - fsize - maximum filesize (KB)
#        - memlock - max locked-in-memory address space (KB)
#        - nofile - max number of open file descriptors
#        - rss - max resident set size (KB)
#        - stack - max stack size (KB)
#        - cpu - max CPU time (MIN)
#        - nproc - max number of processes
#        - as - address space limit (KB)
#        - maxlogins - max number of logins for this user
#        - maxsyslogins - max number of logins on the system
#        - priority - the priority to run user process with
#        - locks - max number of file locks the user can hold
#        - sigpending - max number of pending signals
#        - msgqueue - max memory used by POSIX message queues (bytes)
#        - nice - max nice priority allowed to raise to values: [-20, 19]
#        - rtprio - max realtime priority
#<domain>      <type>  <item>         <value>

#*               soft    core            0
#*               hard    rss             10000
#@student        hard    nproc           20
#@faculty        soft    nproc           20
#@faculty        hard    nproc           50
#ftp             hard    nproc           0
#@student        -       maxlogins       4
# End of file

# oracle-rdbms-server-11gR2-preinstall setting for nofile soft limit is 1024
oracle   soft   nofile    1024

# oracle-rdbms-server-11gR2-preinstall setting for nofile hard limit is 65536
oracle   hard   nofile    65536

# oracle-rdbms-server-11gR2-preinstall setting for nproc soft limit is 16384
# refer orabug15971421 for more info.
oracle   soft   nproc    16384

# oracle-rdbms-server-11gR2-preinstall setting for nproc hard limit is 16384
oracle   hard   nproc    16384

# oracle-rdbms-server-11gR2-preinstall setting for stack soft limit is 10240KB
oracle   soft   stack    10240

# oracle-rdbms-server-11gR2-preinstall setting for stack hard limit is 32768KB
oracle   hard   stack    32768

oracle   hard   memlock    134217728

# oracle-rdbms-server-11gR2-preinstall setting for memlock hard limit is maximum of 128GB on x86_64 or 3GB on x86 OR 90 % of RAM
oracle   soft   memlock    134217728
```

#### 设置防火墙和selinux

```bash
# 继续启用主机防火墙
firewall-cmd --zone=public --add-port=1521/tcp --permanent
firewall-cmd --reload

# 或者关闭主机防火墙
systemctl status firewalld 
systemctl disable firewalld

# 关闭SELINUX
sed -i 's#SELINUX=.*#SELINUX=disabled#g' /etc/selinux/config
sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
grep SELINUX=disabled /etc/selinux/config
setenforce 0
getenforce
```

#### 预设创建目录

```bash
mkdir -p /home/data/oracle 
mkdir -p /home/data/oraInventory
chown -R oracle:oinstall /home/data 
chmod -R 775 /home/data
```

#### 配置主机名

```
hostnamectl set-hostname testdb
cat /etc/sysconfig/network
cat /etc/hosts
echo "hostname=testdb" >> /etc/sysconfig/network
echo "192.168.203.160  testdb" >> /etc/hosts
```

#### 安装配置jdk

将准备好的```jdk-8u261-linux-x64.tar.gz``` 包上传到服务器，然后完成jdk安装配置：

```bash
mkdir -p /usr/java
tar -xzf  jdk-8u261-linux-x64.tar.gz -C /usr/java
```
在/etc/profile增加环境变量
```
export JAVA_HOME=/usr/java/jdk1.8.0_261
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH
export CLASSPATH=.$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$JAVA_HOME/lib/tools.jar
```

#### oracle自身环境变量

切换到oracle用户，修改用户的环境变量配置 ```.bash_profile```，增加如下内容：

```
umask 022
export ORACLE_BASE=/home/data/oracle
export ORACLE_HOME=$ORACLE_BASE/product/11.2.0/db_1
export ORACLE_SID=eportdbtest
export ORACLE_TERM=xterm
export PATH=$ORACLE_HOME/bin:/user/sbin:$PATH
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$ORACLE_HOME/lib64:/lib:/usr/lib:/lib64:/usr/lib64
export LANG=C
export NLS_LANG="SIMPLIFIED CHINESE_CHINA.AL32UTF8"
export NLS_DATE_FORMAT="YYYY-MM-DD HH24:MI:SS"
```

### Oracle11gR2软件安装

#### 解压文件

```bash
mkdir /tmp/oracle
unzip p13390677_112040_Linux-x86-64_1of7.zip -d /tmp/oracle/
unzip p13390677_112040_Linux-x86-64_2of7.zip -d /tmp/oracle/
chown -R oracle:oinstall /tmp/oracle
```
#### 设置显示参数

设置显示参数，在GUI中的终端执行，然后启动安装程序

```bash
# root执行
xhost +

# 切换oracle执行
su - oralce
export DISPLAY=:0.0
```

#### 启动安装程序

```bash
cd /tmp/oracle/database

./runInstaller  -jreLoc /usr/java/jdk1.8.0_261/
```

![](/images/posts/2020/image-20200815130316438.png)


#### 取消安全更新

内部隔离网络，取消在线更新设置
![](/images/posts/2020/image-20200815130647427.png)

#### 更新下载设置

内部隔离网络，跳过软件更新下载

![](/images/posts/2020/image-20200815130739212.png)

#### 安装选项

保持选择第一项，安装同时创建一个数据库

![](/images/posts/2020/image-20200815130843652.png)

#### 系统类别

选择Server Class

![](/images/posts/2020/image-20200815131623881.png)

#### Grid安装选项

实验环境保持默认单实例安装
![](/images/posts/2020/image-20200815131706152.png)

#### 安装类别

保持选择 经典模式安装

![](/images/posts/2020/image-20200815131815232.png)

#### 安装设置

各种路径，数据库名等，与之前环境变量一致，并设置管理密码。

![](/images/posts/2020/image-20200815132222315.png)



#### 环境确认

环境检查会提示缺少```pdksh-5.2.14```，直接忽略，然后继续。

#### 数据库设置助手警告
直接点OK。

![](/images/posts/2020/image-20200815155645263.png)

#### 使用Root执行设置脚本

![](/images/posts/2020/image-20200815160039459.png)
![](/images/posts/2020/image-20200815160057868.png)

执行完脚本后，点击OK完成安装。


#### 确认结果
执行 ```top -u oracle``` 确认Oracle进程已经启动，并且通过 ```lsnrctl status``` 确认监听正常。

![](/images/posts/2020/image-20200815160317741.png)


```bash
[oracle@testdb ~]$ lsnrctl status

LSNRCTL for Linux: Version 11.2.0.4.0 - Production on 15-8月 -2020 23:34:39

Copyright (c) 1991, 2013, Oracle.  All rights reserved.

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=IPC)(KEY=EXTPROC1521)))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 11.2.0.4.0 - Production
Start Date                15-8月 -2020 23:24:43
Uptime                    0 days 0 hr. 9 min. 55 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /home/data/oracle/product/11.2.0/db_1/network/admin/listener.ora
Listener Log File         /home/data/oracle/diag/tnslsnr/testdb/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=testdb)(PORT=1521)))
Services Summary...
Service "eportdbtest" has 1 instance(s).
  Instance "eportdbtest", status READY, has 1 handler(s) for this service...
Service "eportdbtestXDB" has 1 instance(s).
  Instance "eportdbtest", status READY, has 1 handler(s) for this service...
The command completed successfully
```


### 创建实验用表空间和用户

创建一个实验用户：yaoyan

#### 进入管理员

```bash
[oracle@testdb ~]$ sqlplus / as sysdba

SQL*Plus: Release 11.2.0.4.0 Production on 星期六 8月 15 23:42:25 2020

Copyright (c) 1982, 2013, Oracle.  All rights reserved.


Connected to:
Oracle Database 11g Enterprise Edition Release 11.2.0.4.0 - 64bit Production
With the Partitioning, OLAP, Data Mining and Real Application Testing options

SQL>
```


#### 创建临时表空间

```sql
create temporary tablespace yaoyan_temp 
tempfile '/home/oracle/oradata/eportdbtest/yaoyan_temp.dbf' 
size 50m 
autoextend on 
next 50m maxsize 20480m 
extent management local; 
```

#### 创建数据表空间

```sql
create tablespace yaoyan_data 
logging 
datafile '/home/oracle/oradata/eportdbtest/yaoyan_data.dbf' 
size 50m 
autoextend on 
next 50m maxsize 20480m 
extent management local; 
```

#### 创建用户并指定表空间

```sql
create user yaoyan identified by dbpassword 
default tablespace yaoyan_data 
temporary tablespace yaoyan_temp; 
```

#### 给用户授权

```sql
grant connect,resource,dba to yaoyan; 
```

#### 切换用户使用

```sql
SQL> conn yaoyan/dbpassword
Connected.
SQL>
```














