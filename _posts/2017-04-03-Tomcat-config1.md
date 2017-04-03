---
layout: post
title:  "Tomcat运行用户分离安全策略设置"
date:   2017-04-03 11:30:05
categories: 
tags:  tomcat security
excerpt: 根据官方文档推荐，对tomcat进行设置，使运行tomcat的用户和配置管理、应用发布的用户分离。
---

tomcat非配置项的策略问题，参考官方文档:[Non-Tomcat setting](http://tomcat.apache.org/tomcat-7.0-doc/security-howto.html#Non-Tomcat_settings)

大意如下：

>Tomcat配置不应该是唯一的防线。系统中的其他组件（操作系统、网络、数据库等）也应被保护。 
 Tomcat不应在root用户下运行。为Tomcat进程创建专用用户，并向用户提供操作系统的最小必要权限。例如，不可能使用Tomcat用户远程登录。 
 文件权限也应适当限制。以ASF的Tomcat实例为例（那里自动部署被禁用且Web应用程序使用目录部署），标准设置是所有的Tomcat文件为root所有，所属组为Tomcat组，同时所有者拥有读写权限，组只有读权限，其他用户没有任何权限。日志（logs）、临时目录（temp）和工作目录（work）例外，它们属于Tomcat用户而不是root。这意味着，即使攻击者攻陷了Tomcat进程，他们也不能改变Tomcat配置，部署新的Web应用程序或修改现有的Web应用程序。

据此，修改设置如下：

### 1. 为tomcat配置文件和应用发布设置专门用户

1. 我们已决定使用tomcat用户来启动tomcat进程，那么按照官方的安全设置建议，我们需要为tomcat配置文件和tomcat应用发布创建专门的用户：

  ```
[root@test-140 /]# useradd -d /usr/users/tomcatconf  -g usr -m tomcatconf
[root@test-140 /]# useradd -d /usr/users/tomcatapp  -g usr -m tomcatapp
```

### 2. 变更相关文件的所属用户及权限

1. 去掉tomcat文件的other用户所有的权限。

  ```
[tomcat@test-140 apache-tomcat-7.0.76]$ cd ..
[tomcat@test-140 app]$ ll
总用量 8748
drwxr-xr-x. 9 tomcat usr     172 4月   3 11:30 apache-tomcat-7.0.76
-rw-r--r--. 1 tomcat usr 8957288 3月   9 22:08 apache-tomcat-7.0.76.tar.gz
[tomcat@test-140 app]$ chmod -R o-wrx apache-tomcat-7.0.76
[tomcat@test-140 app]$ ll
总用量 8748
drwxr-x---. 9 tomcat usr     172 4月   3 11:30 apache-tomcat-7.0.76
-rw-r--r--. 1 tomcat usr 8957288 3月   9 22:08 apache-tomcat-7.0.76.tar.gz
```

2. 将tomcat下的 **webapps** 的用户修改为tomcatapp，所属组保持为usr，并确保组权限为可读可执行，不能写。

  ```
[root@test-140 apache-tomcat-7.0.76]# chown -R  tomcatapp:usr webapps
[root@test-140 apache-tomcat-7.0.76]# chmod -R  g-w webapps/
[root@test-140 apache-tomcat-7.0.76]# chmod -R  g+rx webapps/
```

3. 将tomcat下其他目录，bin、conf、lib的用户修改为tomcatconf，所属组保持为usr，并确保组权限为可读可执行，不能写。

  ```
[root@test-140 apache-tomcat-7.0.76]# chown -R  tomcatconf:usr  bin  conf  lib
[root@test-140 apache-tomcat-7.0.76]# chmod -R  g-w   bin conf  lib
[root@test-140 apache-tomcat-7.0.76]# chmod -R  g+rx   bin conf  lib
```

tomcat里目录权限变为：

```
[root@test-140 apache-tomcat-7.0.76]# ll
总用量 96
drwxr-x---. 2 tomcatconf usr  4096 4月   3 11:02 bin
drwxr-x---. 3 tomcatconf usr   174 4月   3 10:57 conf
drwxr-x---. 2 tomcatconf usr  4096 4月   3 10:13 lib
-rw-r-----. 1 tomcat     usr 56846 3月   9 21:50 LICENSE
drwxr-x---. 2 tomcat     usr     6 4月   3 10:55 logs
-rw-r-----. 1 tomcat     usr  1239 3月   9 21:50 NOTICE
-rw-r-----. 1 tomcat     usr  8965 3月   9 21:50 RELEASE-NOTES
-rw-r-----. 1 tomcat     usr 16195 3月   9 21:50 RUNNING.txt
drwxr-x---. 2 tomcat     usr    30 4月   3 10:13 temp
-rw-r-----. 1 tomcat     usr     0 4月   3 11:30 test
drwxr-x---. 7 tomcatapp  usr    81 3月   9 21:49 webapps
drwxr-x---. 3 tomcat     usr    22 4月   3 10:14 work
```

此时，除了运行时需要写入的temp、work、logs目录外，其他目录tomcat用户均无法写入，tomcat用户无法再修改conf里的配置文件、bin里的启动脚本、webapps里的应用程序，此时再使用tomcat用户启动tomcat进程。

### 3. 设置tomcat主目录权限，使新建的同组用户能进入操作

因为tomcat部署在相关用户的主目录中，而CentOS的用户主目录默认只有本用户可以进入，所以，使用tomcatapp、tomcatconf登陆时，无法进入tomcat部署目录，无法进行配置修改和应用发布。

```
[tomcatapp@test-140 users]$ ll
总用量 0
drwx------.  6 tomcat     usr 155 4月   3 11:55 tomcat
drwx------.  5 tomcatapp  usr 107 4月   3 12:05 tomcatapp
drwx------.  3 tomcatconf usr  78 4月   3 10:04 tomcatconf
[tomcatapp@test-140 users]$ cd tomcat
-bash: cd: tomcat: 权限不够
[tomcatapp@test-140 users]$ 
```

需要tomcat用户主目录设置同组的读取和执行权限：

```
[root@test-140 users]# chmod g+rx tomcat
```

此时，tomcatapp、tomcatconf这些同组用户就可以进入tomcat的主目录进行相关操作：

```
[tomcatapp@test-140 users]$ ll
总用量 0
drwx------. 10 redis      usr 275 3月  19 23:47 redis
drwx------.  6 sw         usr 155 3月  19 18:20 sw
drwxr-x---.  6 tomcat     usr 155 4月   3 11:55 tomcat
drwx------.  5 tomcatapp  usr 107 4月   3 12:05 tomcatapp
drwx------.  3 tomcatconf usr  78 4月   3 10:04 tomcatconf
[tomcatapp@test-140 users]$ cd tomcat
[tomcatapp@test-140 tomcat]$ ll
总用量 0
drwxr-xr-x. 3 tomcat usr 69 4月   3 10:13 app
[tomcatapp@test-140 tomcat]$ cd app/apache-tomcat-7.0.76/webapps/
[tomcatapp@test-140 webapps]$ ll
总用量 8
drwxr-x---. 14 tomcatapp usr 4096 4月   3 10:13 docs
drwxr-x---.  7 tomcatapp usr  111 4月   3 10:13 examples
drwxr-x---.  5 tomcatapp usr   87 4月   3 10:13 host-manager
drwxr-x---.  5 tomcatapp usr  103 4月   3 10:13 manager
drwxr-x---.  3 tomcatapp usr 4096 4月   3 10:13 ROOT
[tomcatapp@test-140 webapps]$ 
```

这样就实现了tomcat **进程启动、配置管理、应用发布** 权限分离，一定程度增加安全性。






