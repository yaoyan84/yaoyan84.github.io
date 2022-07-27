---
layout: post
title:  "Ubuntu安装MariaDB"
date:   2021-12-10 21:11:32
categories: Server
tags: MariaDB
excerpt: ubuntu安装MariaDB
---


## 1. 安装
```bash
sudo apt install mariadb-server
```

安装完成后自动启动。

```bash
sudo systemctl status mariadb
```
查看服务状态。


## 2. 安全配置

MariaDB 服务器有一个脚本叫做 `mysql_secure_installation` ,用此脚本初始化设置服务器，很容易提高数据库服务器安全。

不带参数运行脚本：

```bash
sudo mysql_secure_installation
```

 * 提示输入数据库root密码，刚装完无密码，回车
 * MariaDB 用户默认使用auth_socket进行鉴权，也不需要创建root密码
 * 删除所有匿名用户
 * 禁止root远程方式登录
 * 删除测试数据库
 * 重载权限
 

```
sudo mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none):
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] n
 ... skipping.

By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] Y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] Y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] Y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] Y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!

```

## 3. root登录

以 root 用户名登录 MariaDB 服务器

```bash
sudo mariadb
```

或者

```bash
sudo mysql
```

进入MariaDB Shell。

## 4. 开始使用

[https://linuxize.com/post/how-to-manage-mysql-databases-and-users-from-the-command-line/](https://linuxize.com/post/how-to-manage-mysql-databases-and-users-from-the-command-line/)

### 4.1. 创建一个数据库

```sql
CREATE DATABASE typecho;
```

或者

```sql
CREATE DATABASE IF NOT EXISTS typecho;
```

### 4.2. 创建一个用户

```sql
CREATE USER 'bloguser'@'localhost' IDENTIFIED BY 'JyMRHLqA5jazMQ3ZWLWz';
```

### 4.3. 为用户授权指定数据库的所有权限

```sql
GRANT ALL PRIVILEGES ON typecho.* TO 'bloguser'@'localhost';
```
