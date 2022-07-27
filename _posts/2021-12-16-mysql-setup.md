---
layout: post
title:  "MySQL基本安装过程"
date:   2021-12-16 19:13:25
categories: Server
tags: mysql
excerpt: MySQL基本安装过程记录
---


## 1. 基本信息：

网络接口：
```
[root@mysql-1 ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:43:16:ef brd ff:ff:ff:ff:ff:ff
    inet 192.168.174.129/24 brd 192.168.174.255 scope global noprefixroute dynamic ens33
       valid_lft 1475sec preferred_lft 1475sec
    inet6 fe80::6000:2d5d:bc9:b4d3/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: ens37: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:43:16:f9 brd ff:ff:ff:ff:ff:ff
    inet 192.168.203.181/24 brd 192.168.203.255 scope global noprefixroute ens37
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fe43:16f9/64 scope link
       valid_lft forever preferred_lft forever
```

内核：

```
[root@mysql-1 ~]# uname -a
Linux mysql-1 3.10.0-957.el7.x86_64 #1 SMP Thu Nov 8 23:39:32 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

操作系统：

```
cat /etc/system-release
CentOS Linux release 7.9.2009 (Core)
```

## 2. 准备MySQL  RPM包

下载地址： [https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)


找到对应版本下载即可。本例直接wget下载：

```
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.27-1.el7.x86_64.rpm-bundle.tar
```

解压缩：

```
mkdir mysql
tar xvf mysql-8.0.27-1.el7.x86_64.rpm-bundle.tar  -C ./mysql
cd mysql
ll
```
得到RPM文件：
```
[root@mysql-1 mysql]# ll
total 817720
-rw-r--r--. 1 7155 31415  55178328 Sep 29 15:33 mysql-community-client-8.0.27-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415   5932800 Sep 29 15:34 mysql-community-client-plugins-8.0.27-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415    641616 Sep 29 15:34 mysql-community-common-8.0.27-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415   7760100 Sep 29 15:34 mysql-community-devel-8.0.27-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415  23637616 Sep 29 15:34 mysql-community-embedded-compat-8.0.27-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415   4935900 Sep 29 15:34 mysql-community-libs-8.0.27-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415   1264256 Sep 29 15:34 mysql-community-libs-compat-8.0.27-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415 470252428 Sep 29 15:36 mysql-community-server-8.0.27-1.el7.x86_64.rpm
-rw-r--r--. 1 7155 31415 267724484 Sep 29 15:38 mysql-community-test-8.0.27-1.el7.x86_64.rpm
```

作为数据库服务器，需要安装server包：
```
sudo yum install mysql-community-{server,client,common,libs}-*
```

作为应用客户端安装：
```
sudo yum install mysql-community-{client,common,libs}-*
```

安装过程

```
[root@mysql-1 mysql]# sudo yum install mysql-community-{server,client,common,libs}-*
Loaded plugins: fastestmirror
Examining mysql-community-server-8.0.27-1.el7.x86_64.rpm: mysql-community-server-8.0.27-1.el7.x86_64
Marking mysql-community-server-8.0.27-1.el7.x86_64.rpm to be installed
Examining mysql-community-client-8.0.27-1.el7.x86_64.rpm: mysql-community-client-8.0.27-1.el7.x86_64
Marking mysql-community-client-8.0.27-1.el7.x86_64.rpm to be installed
Examining mysql-community-client-plugins-8.0.27-1.el7.x86_64.rpm: mysql-community-client-plugins-8.0.27-1.el7.x86_64
Marking mysql-community-client-plugins-8.0.27-1.el7.x86_64.rpm to be installed
Examining mysql-community-common-8.0.27-1.el7.x86_64.rpm: mysql-community-common-8.0.27-1.el7.x86_64
Marking mysql-community-common-8.0.27-1.el7.x86_64.rpm to be installed
Examining mysql-community-libs-8.0.27-1.el7.x86_64.rpm: mysql-community-libs-8.0.27-1.el7.x86_64
Marking mysql-community-libs-8.0.27-1.el7.x86_64.rpm to be installed
Examining mysql-community-libs-compat-8.0.27-1.el7.x86_64.rpm: mysql-community-libs-compat-8.0.27-1.el7.x86_64
Marking mysql-community-libs-compat-8.0.27-1.el7.x86_64.rpm to be installed
Resolving Dependencies
--> Running transaction check
---> Package mariadb-libs.x86_64 1:5.5.68-1.el7 will be obsoleted
---> Package mysql-community-client.x86_64 0:8.0.27-1.el7 will be installed
---> Package mysql-community-client-plugins.x86_64 0:8.0.27-1.el7 will be installed
---> Package mysql-community-common.x86_64 0:8.0.27-1.el7 will be installed
---> Package mysql-community-libs.x86_64 0:8.0.27-1.el7 will be obsoleting
---> Package mysql-community-libs-compat.x86_64 0:8.0.27-1.el7 will be obsoleting
---> Package mysql-community-server.x86_64 0:8.0.27-1.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===================================================================================================================================
 Package                             Arch        Version            Repository                                                Size
===================================================================================================================================
Installing:
 mysql-community-client              x86_64      8.0.27-1.el7       /mysql-community-client-8.0.27-1.el7.x86_64              262 M
 mysql-community-client-plugins      x86_64      8.0.27-1.el7       /mysql-community-client-plugins-8.0.27-1.el7.x86_64       28 M
 mysql-community-common              x86_64      8.0.27-1.el7       /mysql-community-common-8.0.27-1.el7.x86_64              9.5 M
 mysql-community-libs                x86_64      8.0.27-1.el7       /mysql-community-libs-8.0.27-1.el7.x86_64                 24 M
     replacing  mariadb-libs.x86_64 1:5.5.68-1.el7
 mysql-community-libs-compat         x86_64      8.0.27-1.el7       /mysql-community-libs-compat-8.0.27-1.el7.x86_64         6.0 M
     replacing  mariadb-libs.x86_64 1:5.5.68-1.el7
 mysql-community-server              x86_64      8.0.27-1.el7       /mysql-community-server-8.0.27-1.el7.x86_64              2.1 G

Transaction Summary
===================================================================================================================================
Install  6 Packages

Total size: 2.4 G
Is this ok [y/d/N]: y
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : mysql-community-client-plugins-8.0.27-1.el7.x86_64                                                              1/7
  Installing : mysql-community-common-8.0.27-1.el7.x86_64                                                                      2/7
  Installing : mysql-community-libs-8.0.27-1.el7.x86_64                                                                        3/7
  Installing : mysql-community-client-8.0.27-1.el7.x86_64                                                                      4/7
  Installing : mysql-community-server-8.0.27-1.el7.x86_64                                                                      5/7
  Installing : mysql-community-libs-compat-8.0.27-1.el7.x86_64                                                                 6/7
  Erasing    : 1:mariadb-libs-5.5.68-1.el7.x86_64                                                                              7/7
  Verifying  : mysql-community-libs-8.0.27-1.el7.x86_64                                                                        1/7
  Verifying  : mysql-community-common-8.0.27-1.el7.x86_64                                                                      2/7
  Verifying  : mysql-community-client-plugins-8.0.27-1.el7.x86_64                                                              3/7
  Verifying  : mysql-community-server-8.0.27-1.el7.x86_64                                                                      4/7
  Verifying  : mysql-community-client-8.0.27-1.el7.x86_64                                                                      5/7
  Verifying  : mysql-community-libs-compat-8.0.27-1.el7.x86_64                                                                 6/7
  Verifying  : 1:mariadb-libs-5.5.68-1.el7.x86_64                                                                              7/7

Installed:
  mysql-community-client.x86_64 0:8.0.27-1.el7                   mysql-community-client-plugins.x86_64 0:8.0.27-1.el7
  mysql-community-common.x86_64 0:8.0.27-1.el7                   mysql-community-libs.x86_64 0:8.0.27-1.el7
  mysql-community-libs-compat.x86_64 0:8.0.27-1.el7              mysql-community-server.x86_64 0:8.0.27-1.el7

Replaced:
  mariadb-libs.x86_64 1:5.5.68-1.el7

Complete!
```

安装完成，Mysql服务不会自动启动：

```
[root@mysql-1 mysql]# systemctl status mysqld
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
```

使用RPM安装后，一些资源的默认位置：

|              文件或资源              |            位置             |
| ----------------------------------- | -------------------------- |
| 客户端程序和脚本                      | /usr/bin                   |
| mysqld 服务                          | /usr/sbin                  |
| 配置文件                             | /etc/my.cnf                |
| 数据目录                             | /var/lib/mysql             |
| 错误日志文件                         | RHEL： /var/log/mysqld.log |
| secure_file_priv 值                 | /var/lib/mysql-files       |
| 系统服务初始化脚本                    | RHEL:  /etc/init.d/mysqld  |
| 系统服务                             | mysqld                     |
| Pid文件                              | /var/run/mysql/mysqld.pid  |
| Socket                              | /var/lib/mysql/mysql.sock  |
| Keyring 钥匙环目录                   | /var/lib/mysql-keyring     |
| 用户手册                             | /usr/share/man             |
| 头文件                               | /usr/include/mysql         |
| 库文件                               | /usr/lib/mysql             |
| 其他支持文件(错误消息、角色设置文件等) | /usr/share/mysql           |

同时，采用RPM安装会自动创建mysql用户和mysql用户组。


## 3. 启动mysql及初始化

启动服务

```
systemctl start mysqld
```

第一次启动服务，会在日志中打印出临时的root密码：

```
cat /var/log/mysqld.log
2021-12-16T09:10:50.210028Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.27) initializing of server in progress as process 75876
2021-12-16T09:10:50.247429Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2021-12-16T09:10:50.966990Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2021-12-16T09:10:52.089170Z 0 [Warning] [MY-013746] [Server] A deprecated TLS version TLSv1 is enabled for channel mysql_main
2021-12-16T09:10:52.089191Z 0 [Warning] [MY-013746] [Server] A deprecated TLS version TLSv1.1 is enabled for channel mysql_main
2021-12-16T09:10:52.335377Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: OIwqgfXqw9<x
2021-12-16T09:10:55.430854Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.27) starting as process 75923
2021-12-16T09:10:55.450371Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2021-12-16T09:10:55.874854Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2021-12-16T09:10:56.219276Z 0 [Warning] [MY-013746] [Server] A deprecated TLS version TLSv1 is enabled for channel mysql_main
2021-12-16T09:10:56.219301Z 0 [Warning] [MY-013746] [Server] A deprecated TLS version TLSv1.1 is enabled for channel mysql_main
2021-12-16T09:10:56.220460Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2021-12-16T09:10:56.220501Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2021-12-16T09:10:56.242979Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.27'  socket: '/var/lib/mysql/mysql.sock'  port: 3306  MySQL Community Server - GPL.
2021-12-16T09:10:56.243098Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
```

如上：
```
A temporary password is generated for root@localhost: OIwqgfXqw9<x
```

进入mysql数据库，输入以下内容，回车后输入以上默认密码，看到 `mysql>` 提示符及进入mysql数据库
```
[root@mysql-1 ~]# mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.0.27

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

此时需要先修改默认root@localhost用户的临时密码，否则无法进行其他操作，会提示：
```
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
```

修改默认的密码时，密码默认有复杂度要求，简单密码默认无法设置。


```SQL
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'dbwork';
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'DBw0Rk!2021';
Query OK, 0 rows affected (0.00 sec)

mysql>
```

> 密码验证功能说明：[https://dev.mysql.com/doc/refman/8.0/en/validate-password.html](https://dev.mysql.com/doc/refman/8.0/en/validate-password.html)
> 密码至少包含一个大写字母，一个小写字母，一个数字和一个特殊字符，并且密码长度最少为8个字符。


修改完后可以进行其他操作：
```sql
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)

mysql> use mysql;
Database changed
mysql> select host,user from user;
+-----------+------------------+
| host      | user             |
+-----------+------------------+
| localhost | mysql.infoschema |
| localhost | mysql.session    |
| localhost | mysql.sys        |
| localhost | root             |
+-----------+------------------+
4 rows in set (0.00 sec)
```

## 4. 创建库和用户
一般应用系统，应用和数据库分开部署，应用一般采用分布式部署。

需求：
  * 创建数据库 app1
  * 创建可远程访问数据库的用户 user_app1
  * user_app1拥有数据库app1的全部权限
 
创建数据库：
```sql
CREATE DATABASE app1;
```

创建用户：
```sql
CREATE USER 'user_app1'@'%' IDENTIFIED BY 'App1#Test';
```

用户授权：
```sql
GRANT ALL PRIVILEGES ON app1.* TO 'user_app1'@'%';
```
> % 为host的通配符，* 为数据库和表的通配符。

刷新权限表：
```sql
flush privileges;
```

查看某用户的权限：
```sql
show grants for 'user_app1'@'%';
```

> 取消授权使用 `revoke ...  on ... from  ...` 语句。
