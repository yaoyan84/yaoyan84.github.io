---
layout: post
title:  "Docker学习笔记（6）"
date:   2017-06-16 22:34:05
categories: Docker
tags: Docker
excerpt: Docker学习。
---

### 用Dockerfile 构建镜像

Docker不推荐使用```docker commit``` 构建镜像，推荐使用Dokerfile定义文件和```docker build```命令来构建镜像。

Dockerfile使用基本的基于DSL（Domain Specific Language）语法的指令来构建一个Docker镜像，更具有可重复性、透明性以及幂等性。

### 第一个Dockerfile

```
mkdir static_web
cd static_web
touch Dockerfile
```

创建一个目录，作为构建环境，在其中编写Dockerfile文件，此环境Docker也称为构建上下文。
Docker会在构建镜像时，将上下文和上下文中的文件和目录上传。

```
# Version: 0.0.1
FROM ubuntu:latest
MAINTAINER   Yao Yan "callia.yao@gmail.com"
RUN apt-get update && apt-get install -y nginx
RUN echo 'Hi, I am in your container' \
        >/usr/share/nginx/html/index.html
EXPOSE 80
```

Dockerfile由一系列指令和参数组成。每个指令都必须是大写字母，切后面要跟一个参数： FROM ubuntu:latest

Dockerfile中的指令会按顺序从上往下执行，所以应该根据需要合理安排指令的顺义。

* ```#``` 注释行
* Dockerfile 必须从FROM开始，指定基础镜像

**Docker执行Dockerfile流程**

* Docker从基础镜像运行一个容器
* 执行一条指令，对容器进行修改
* 执行类似docker commit的操作，提交一个新的镜像层
* Docker再基于刚提交的镜像运行一个新的容器
* 执行Dockerfile中的下一条指令，直到所有指令都执行完毕

如果因为某些原因，在其中某条整理失败，用户也将得到一个可以使用的镜像，可以用来调试，看下一个指令为什么会失败。

* FROM指定基础镜像
* MAINTAINER 设置作者和邮箱
* RUN 执行命令修改镜像，一个RUN指令会提交一个镜像层，命令用/bin/sh -c 封装，也可以使用exec格式的RUN指令
* EXPOSE指令指定向外部公开的端口（只是设置指定，并不会自动打开，打开需要docker run由镜像创建容器时加参数设置）

### 运行Dockerfile构建镜像

```
cd static_web
docker build -t="yaoyan84/static_web:v1" .
```

即：  ```docker build -t="用户名/仓库名:tag"  Dockerfile文件所在目录```

范例中使用“.”表示在当前目录，目录也可以是一个git仓库地址

自从Docker 1.5.0开始，使用 -f 参数，可以指定Dockerfile文件，文件名可以不叫Dockerfile

```
docker build -t="yaoyan84/static_web:v1"  -f path/to/file
```

构建上下文将被上传至镜像，如果存在 .dockerignore，则会根据规则进行忽略，类似git的.gitignore文件

```
[root@yaoyantest1 static_web]# docker build -t="yaoyan84/static_web:v1" .
Sending build context to Docker daemon 4.608 kB
Step 1/5 : FROM ubuntu:latest
 ---> 7b9b13f7b9c0
Step 2/5 : MAINTAINER Yao Yan "callia.yao@gmail.com"
 ---> Running in b67cd5bb56d2
 ---> d8f15c2678f9
Removing intermediate container b67cd5bb56d2
Step 3/5 : RUN apt-get update && apt-get install -y nginx
 ---> Running in 722bdb146b33
Get:1 http://archive.ubuntu.com/ubuntu xenial InRelease [247 kB]
Get:2 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
Get:4 http://security.ubuntu.com/ubuntu xenial-security/universe Sources [35.1 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
Get:6 http://security.ubuntu.com/ubuntu xenial-security/main amd64 Packages [354 kB]
Get:7 http://archive.ubuntu.com/ubuntu xenial/universe Sources [9802 kB]
Get:8 http://security.ubuntu.com/ubuntu xenial-security/restricted amd64 Packages [12.8 kB]
Get:9 http://security.ubuntu.com/ubuntu xenial-security/universe amd64 Packages [161 kB]
Get:10 http://security.ubuntu.com/ubuntu xenial-security/multiverse amd64 Packages [2932 B]
Get:11 http://archive.ubuntu.com/ubuntu xenial/main amd64 Packages [1558 kB]
Get:12 http://archive.ubuntu.com/ubuntu xenial/restricted amd64 Packages [14.1 kB]
Get:13 http://archive.ubuntu.com/ubuntu xenial/universe amd64 Packages [9827 kB]
Get:14 http://archive.ubuntu.com/ubuntu xenial/multiverse amd64 Packages [176 kB]
Get:15 http://archive.ubuntu.com/ubuntu xenial-updates/universe Sources [196 kB]
Get:16 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 Packages [703 kB]
Get:17 http://archive.ubuntu.com/ubuntu xenial-updates/restricted amd64 Packages [13.2 kB]
Get:18 http://archive.ubuntu.com/ubuntu xenial-updates/universe amd64 Packages [603 kB]
Get:19 http://archive.ubuntu.com/ubuntu xenial-updates/multiverse amd64 Packages [9810 B]
Get:20 http://archive.ubuntu.com/ubuntu xenial-backports/main amd64 Packages [4927 B]
Get:21 http://archive.ubuntu.com/ubuntu xenial-backports/universe amd64 Packages [6032 B]
Fetched 24.0 MB in 45s (531 kB/s)
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
The following additional packages will be installed:
  fontconfig-config fonts-dejavu-core geoip-database libexpat1 libfontconfig1
  libfreetype6 libgd3 libgeoip1 libicu55 libjbig0 libjpeg-turbo8 libjpeg8
  libpng12-0 libssl1.0.0 libtiff5 libvpx3 libx11-6 libx11-data libxau6 libxcb1
  libxdmcp6 libxml2 libxpm4 libxslt1.1 nginx-common nginx-core sgml-base ucf
  xml-core
Suggested packages:
  libgd-tools geoip-bin fcgiwrap nginx-doc ssl-cert sgml-base-doc debhelper
The following NEW packages will be installed:
  fontconfig-config fonts-dejavu-core geoip-database libexpat1 libfontconfig1
  libfreetype6 libgd3 libgeoip1 libicu55 libjbig0 libjpeg-turbo8 libjpeg8
  libpng12-0 libssl1.0.0 libtiff5 libvpx3 libx11-6 libx11-data libxau6 libxcb1
  libxdmcp6 libxml2 libxpm4 libxslt1.1 nginx nginx-common nginx-core sgml-base
  ucf xml-core
0 upgraded, 30 newly installed, 0 to remove and 0 not upgraded.
Need to get 15.5 MB of archives.
After this operation, 57.3 MB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 libxau6 amd64 1:1.0.8-1 [8376 B]
Get:2 http://archive.ubuntu.com/ubuntu xenial/main amd64 sgml-base all 1.26+nmu4ubuntu1 [12.5 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial/main amd64 libjpeg-turbo8 amd64 1.4.2-0ubuntu3 [111 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial/main amd64 libjbig0 amd64 2.1-3.1 [26.6 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libexpat1 amd64 2.1.0-7ubuntu0.16.04.2 [71.3 kB]
Get:6 http://archive.ubuntu.com/ubuntu xenial/main amd64 libpng12-0 amd64 1.2.54-1ubuntu1 [116 kB]
Get:7 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libssl1.0.0 amd64 1.0.2g-1ubuntu4.8 [1081 kB]
Get:8 http://archive.ubuntu.com/ubuntu xenial/main amd64 ucf all 3.0036 [52.9 kB]
Get:9 http://archive.ubuntu.com/ubuntu xenial/main amd64 geoip-database all 20160408-1 [1678 kB]
Get:10 http://archive.ubuntu.com/ubuntu xenial/main amd64 libgeoip1 amd64 1.6.9-1 [70.1 kB]
Get:11 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libicu55 amd64 55.1-7ubuntu0.2 [7659 kB]
Get:12 http://archive.ubuntu.com/ubuntu xenial/main amd64 libxdmcp6 amd64 1:1.1.2-1.1 [11.0 kB]
Get:13 http://archive.ubuntu.com/ubuntu xenial/main amd64 libxcb1 amd64 1.11.1-1ubuntu1 [40.0 kB]
Get:14 http://archive.ubuntu.com/ubuntu xenial/main amd64 libx11-data all 2:1.6.3-1ubuntu2 [113 kB]
Get:15 http://archive.ubuntu.com/ubuntu xenial/main amd64 libx11-6 amd64 2:1.6.3-1ubuntu2 [571 kB]
Get:16 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libxml2 amd64 2.9.3+dfsg1-1ubuntu0.2 [697 kB]
Get:17 http://archive.ubuntu.com/ubuntu xenial/main amd64 xml-core all 0.13+nmu2 [23.3 kB]
Get:18 http://archive.ubuntu.com/ubuntu xenial/main amd64 fonts-dejavu-core all 2.35-1 [1039 kB]
Get:19 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 fontconfig-config all 2.11.94-0ubuntu1.1 [49.9 kB]
Get:20 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libfreetype6 amd64 2.6.1-0.1ubuntu2.3 [316 kB]
Get:21 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libfontconfig1 amd64 2.11.94-0ubuntu1.1 [131 kB]
Get:22 http://archive.ubuntu.com/ubuntu xenial/main amd64 libjpeg8 amd64 8c-2ubuntu8 [2194 B]
Get:23 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libtiff5 amd64 4.0.6-1ubuntu0.2 [146 kB]
Get:24 http://archive.ubuntu.com/ubuntu xenial/main amd64 libvpx3 amd64 1.5.0-2ubuntu1 [732 kB]
Get:25 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libxpm4 amd64 1:3.5.11-1ubuntu0.16.04.1 [33.8 kB]
Get:26 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libgd3 amd64 2.1.1-4ubuntu0.16.04.6 [126 kB]
Get:27 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libxslt1.1 amd64 1.1.28-2.1ubuntu0.1 [145 kB]
Get:28 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 nginx-common all 1.10.0-0ubuntu0.16.04.4 [26.6 kB]
Get:29 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 nginx-core amd64 1.10.0-0ubuntu0.16.04.4 [428 kB]
Get:30 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 nginx all 1.10.0-0ubuntu0.16.04.4 [3498 B]
debconf: delaying package configuration, since apt-utils is not installed
Fetched 15.5 MB in 35s (436 kB/s)
Selecting previously unselected package libxau6:amd64.
(Reading database ... 4764 files and directories currently installed.)
Preparing to unpack .../libxau6_1%3a1.0.8-1_amd64.deb ...
Unpacking libxau6:amd64 (1:1.0.8-1) ...
Selecting previously unselected package sgml-base.
Preparing to unpack .../sgml-base_1.26+nmu4ubuntu1_all.deb ...
Unpacking sgml-base (1.26+nmu4ubuntu1) ...
Selecting previously unselected package libjpeg-turbo8:amd64.
Preparing to unpack .../libjpeg-turbo8_1.4.2-0ubuntu3_amd64.deb ...
Unpacking libjpeg-turbo8:amd64 (1.4.2-0ubuntu3) ...
Selecting previously unselected package libjbig0:amd64.
Preparing to unpack .../libjbig0_2.1-3.1_amd64.deb ...
Unpacking libjbig0:amd64 (2.1-3.1) ...
Selecting previously unselected package libexpat1:amd64.
Preparing to unpack .../libexpat1_2.1.0-7ubuntu0.16.04.2_amd64.deb ...
Unpacking libexpat1:amd64 (2.1.0-7ubuntu0.16.04.2) ...
Selecting previously unselected package libpng12-0:amd64.
Preparing to unpack .../libpng12-0_1.2.54-1ubuntu1_amd64.deb ...
Unpacking libpng12-0:amd64 (1.2.54-1ubuntu1) ...
Selecting previously unselected package libssl1.0.0:amd64.
Preparing to unpack .../libssl1.0.0_1.0.2g-1ubuntu4.8_amd64.deb ...
Unpacking libssl1.0.0:amd64 (1.0.2g-1ubuntu4.8) ...
Selecting previously unselected package ucf.
Preparing to unpack .../archives/ucf_3.0036_all.deb ...
Moving old data out of the way
Unpacking ucf (3.0036) ...
Selecting previously unselected package geoip-database.
Preparing to unpack .../geoip-database_20160408-1_all.deb ...
Unpacking geoip-database (20160408-1) ...
Selecting previously unselected package libgeoip1:amd64.
Preparing to unpack .../libgeoip1_1.6.9-1_amd64.deb ...
Unpacking libgeoip1:amd64 (1.6.9-1) ...
Selecting previously unselected package libicu55:amd64.
Preparing to unpack .../libicu55_55.1-7ubuntu0.2_amd64.deb ...
Unpacking libicu55:amd64 (55.1-7ubuntu0.2) ...
Selecting previously unselected package libxdmcp6:amd64.
Preparing to unpack .../libxdmcp6_1%3a1.1.2-1.1_amd64.deb ...
Unpacking libxdmcp6:amd64 (1:1.1.2-1.1) ...
Selecting previously unselected package libxcb1:amd64.
Preparing to unpack .../libxcb1_1.11.1-1ubuntu1_amd64.deb ...
Unpacking libxcb1:amd64 (1.11.1-1ubuntu1) ...
Selecting previously unselected package libx11-data.
Preparing to unpack .../libx11-data_2%3a1.6.3-1ubuntu2_all.deb ...
Unpacking libx11-data (2:1.6.3-1ubuntu2) ...
Selecting previously unselected package libx11-6:amd64.
Preparing to unpack .../libx11-6_2%3a1.6.3-1ubuntu2_amd64.deb ...
Unpacking libx11-6:amd64 (2:1.6.3-1ubuntu2) ...
Selecting previously unselected package libxml2:amd64.
Preparing to unpack .../libxml2_2.9.3+dfsg1-1ubuntu0.2_amd64.deb ...
Unpacking libxml2:amd64 (2.9.3+dfsg1-1ubuntu0.2) ...
Selecting previously unselected package xml-core.
Preparing to unpack .../xml-core_0.13+nmu2_all.deb ...
Unpacking xml-core (0.13+nmu2) ...
Selecting previously unselected package fonts-dejavu-core.
Preparing to unpack .../fonts-dejavu-core_2.35-1_all.deb ...
Unpacking fonts-dejavu-core (2.35-1) ...
Selecting previously unselected package fontconfig-config.
Preparing to unpack .../fontconfig-config_2.11.94-0ubuntu1.1_all.deb ...
Unpacking fontconfig-config (2.11.94-0ubuntu1.1) ...
Selecting previously unselected package libfreetype6:amd64.
Preparing to unpack .../libfreetype6_2.6.1-0.1ubuntu2.3_amd64.deb ...
Unpacking libfreetype6:amd64 (2.6.1-0.1ubuntu2.3) ...
Selecting previously unselected package libfontconfig1:amd64.
Preparing to unpack .../libfontconfig1_2.11.94-0ubuntu1.1_amd64.deb ...
Unpacking libfontconfig1:amd64 (2.11.94-0ubuntu1.1) ...
Selecting previously unselected package libjpeg8:amd64.
Preparing to unpack .../libjpeg8_8c-2ubuntu8_amd64.deb ...
Unpacking libjpeg8:amd64 (8c-2ubuntu8) ...
Selecting previously unselected package libtiff5:amd64.
Preparing to unpack .../libtiff5_4.0.6-1ubuntu0.2_amd64.deb ...
Unpacking libtiff5:amd64 (4.0.6-1ubuntu0.2) ...
Selecting previously unselected package libvpx3:amd64.
Preparing to unpack .../libvpx3_1.5.0-2ubuntu1_amd64.deb ...
Unpacking libvpx3:amd64 (1.5.0-2ubuntu1) ...
Selecting previously unselected package libxpm4:amd64.
Preparing to unpack .../libxpm4_1%3a3.5.11-1ubuntu0.16.04.1_amd64.deb ...
Unpacking libxpm4:amd64 (1:3.5.11-1ubuntu0.16.04.1) ...
Selecting previously unselected package libgd3:amd64.
Preparing to unpack .../libgd3_2.1.1-4ubuntu0.16.04.6_amd64.deb ...
Unpacking libgd3:amd64 (2.1.1-4ubuntu0.16.04.6) ...
Selecting previously unselected package libxslt1.1:amd64.
Preparing to unpack .../libxslt1.1_1.1.28-2.1ubuntu0.1_amd64.deb ...
Unpacking libxslt1.1:amd64 (1.1.28-2.1ubuntu0.1) ...
Selecting previously unselected package nginx-common.
Preparing to unpack .../nginx-common_1.10.0-0ubuntu0.16.04.4_all.deb ...
Unpacking nginx-common (1.10.0-0ubuntu0.16.04.4) ...
Selecting previously unselected package nginx-core.
Preparing to unpack .../nginx-core_1.10.0-0ubuntu0.16.04.4_amd64.deb ...
Unpacking nginx-core (1.10.0-0ubuntu0.16.04.4) ...
Selecting previously unselected package nginx.
Preparing to unpack .../nginx_1.10.0-0ubuntu0.16.04.4_all.deb ...
Unpacking nginx (1.10.0-0ubuntu0.16.04.4) ...
Processing triggers for libc-bin (2.23-0ubuntu7) ...
Processing triggers for systemd (229-4ubuntu17) ...
Setting up libxau6:amd64 (1:1.0.8-1) ...
Setting up sgml-base (1.26+nmu4ubuntu1) ...
Setting up libjpeg-turbo8:amd64 (1.4.2-0ubuntu3) ...
Setting up libjbig0:amd64 (2.1-3.1) ...
Setting up libexpat1:amd64 (2.1.0-7ubuntu0.16.04.2) ...
Setting up libpng12-0:amd64 (1.2.54-1ubuntu1) ...
Setting up libssl1.0.0:amd64 (1.0.2g-1ubuntu4.8) ...
debconf: unable to initialize frontend: Dialog
debconf: (TERM is not set, so the dialog frontend is not usable.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.22.1 /usr/lo
cal/share/perl/5.22.1 /usr/lib/x86_64-linux-gnu/perl5/5.22 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.22 /usr/share/perl/5.22 /usr/local/lib/site_perl /usr/lib/x86_64-linux-gnu/perl-base .) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 7.)debconf: falling back to frontend: Teletype
Setting up ucf (3.0036) ...
debconf: unable to initialize frontend: Dialog
debconf: (TERM is not set, so the dialog frontend is not usable.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.22.1 /usr/lo
cal/share/perl/5.22.1 /usr/lib/x86_64-linux-gnu/perl5/5.22 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.22 /usr/share/perl/5.22 /usr/local/lib/site_perl /usr/lib/x86_64-linux-gnu/perl-base .) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 7.)debconf: falling back to frontend: Teletype
Setting up geoip-database (20160408-1) ...
Setting up libgeoip1:amd64 (1.6.9-1) ...
Setting up libicu55:amd64 (55.1-7ubuntu0.2) ...
Setting up libxdmcp6:amd64 (1:1.1.2-1.1) ...
Setting up libxcb1:amd64 (1.11.1-1ubuntu1) ...
Setting up libx11-data (2:1.6.3-1ubuntu2) ...
Setting up libx11-6:amd64 (2:1.6.3-1ubuntu2) ...
Setting up libxml2:amd64 (2.9.3+dfsg1-1ubuntu0.2) ...
Setting up xml-core (0.13+nmu2) ...
Setting up fonts-dejavu-core (2.35-1) ...
Setting up fontconfig-config (2.11.94-0ubuntu1.1) ...
Setting up libfreetype6:amd64 (2.6.1-0.1ubuntu2.3) ...
Setting up libfontconfig1:amd64 (2.11.94-0ubuntu1.1) ...
Setting up libjpeg8:amd64 (8c-2ubuntu8) ...
Setting up libtiff5:amd64 (4.0.6-1ubuntu0.2) ...
Setting up libvpx3:amd64 (1.5.0-2ubuntu1) ...
Setting up libxpm4:amd64 (1:3.5.11-1ubuntu0.16.04.1) ...
Setting up libgd3:amd64 (2.1.1-4ubuntu0.16.04.6) ...
Setting up libxslt1.1:amd64 (1.1.28-2.1ubuntu0.1) ...
Setting up nginx-common (1.10.0-0ubuntu0.16.04.4) ...
debconf: unable to initialize frontend: Dialog
debconf: (TERM is not set, so the dialog frontend is not usable.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.22.1 /usr/lo
cal/share/perl/5.22.1 /usr/lib/x86_64-linux-gnu/perl5/5.22 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.22 /usr/share/perl/5.22 /usr/local/lib/site_perl /usr/lib/x86_64-linux-gnu/perl-base .) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 7.)debconf: falling back to frontend: Teletype
Setting up nginx-core (1.10.0-0ubuntu0.16.04.4) ...
invoke-rc.d: could not determine current runlevel
invoke-rc.d: policy-rc.d denied execution of start.
Setting up nginx (1.10.0-0ubuntu0.16.04.4) ...
Processing triggers for libc-bin (2.23-0ubuntu7) ...
Processing triggers for sgml-base (1.26+nmu4ubuntu1) ...
Processing triggers for systemd (229-4ubuntu17) ...
 ---> ca7420f54186
Removing intermediate container 722bdb146b33
Step 4/5 : RUN echo 'Hi, I am in your container'         >/usr/share/nginx/html/index.html
 ---> Running in 4bc485a2edce
 ---> 5b4f6de97560
Removing intermediate container 4bc485a2edce
Step 5/5 : EXPOSE 80
 ---> Running in 6cec003bcaaa
 ---> f347ab4e79e4
Removing intermediate container 6cec003bcaaa
Successfully built f347ab4e79e4
[root@yaoyantest1 static_web]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED              SIZE
yaoyan84/static_web   v1                  f347ab4e79e4        About a minute ago   213 MB
yaoyan84/nginx        webproxy            c12d0aedecfd        16 hours ago         266 MB
ubuntu                latest              7b9b13f7b9c0        9 days ago           118 MB
hello-world           latest              48b5124b2768        4 months ago         1.84 kB
[root@yaoyantest1 static_web]#  
```

* --no-cache 忽略缓存，构建失败重新构建，或多次构建时，忽略缓存中已完成的指令

### 查看镜像的构建历史： ```docker history```

```
[root@yaoyantest1 ~]# docker history yaoyan84/static_web:v1
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
f347ab4e79e4        18 minutes ago      /bin/sh -c #(nop)  EXPOSE 80/tcp                0 B                 
5b4f6de97560        18 minutes ago      /bin/sh -c echo 'Hi, I am in your containe...   27 B               
ca7420f54186        18 minutes ago      /bin/sh -c apt-get update && apt-get insta...   95 MB               
d8f15c2678f9        20 minutes ago      /bin/sh -c #(nop)  MAINTAINER Yao Yan "cal...   0 B                 
7b9b13f7b9c0        9 days ago          /bin/sh -c #(nop)  CMD ["/bin/bash"]            0 B                 
<missing>           9 days ago          /bin/sh -c mkdir -p /run/systemd && echo '...   7 B                 
<missing>           9 days ago          /bin/sh -c sed -i 's/^#\s*\(deb.*universe\...   2.76 kB             
<missing>           9 days ago          /bin/sh -c rm -rf /var/lib/apt/lists/*          0 B                 
<missing>           9 days ago          /bin/sh -c set -xe   && echo '#!/bin/sh' >...   745 B               
<missing>           9 days ago          /bin/sh -c #(nop) ADD file:5aff8c59a707833...   118 MB             
```

可以看见每一个命令新加的文件系统层的ID

*<学习用书《第一本Docker书》James Turnbull>*
