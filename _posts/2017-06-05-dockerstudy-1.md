---
layout: post
title:  "Docker学习笔记（1）"
date:   2017-06-05 22:46:05
categories: Docker
tags: Dcoker
excerpt: 学习Docker容器技术。
---

## 1. docker安装

Docker官方安装指南：  [https://docs.docker.com/engine/installation/](https://docs.docker.com/engine/installation/)

Docker有两种版本: **社区版Community Edition (CE)** 和  **企业版 Enterprise Edition (EE)**.

**Doker社区版 (CE)** 对开发人员和小团队来说是理想的选择，他们希望开始使用Docker并尝试基于容器的应用程序.

Docker CE 有两个升级通道, stable版 和 edge版:
 * Stable版每个季度提供可靠的更新
 * Edge版每月提供最新的功能

查看 [Docker Community Edition](https://www.docker.com/community-edition)页面 获取更多Docker CE的信息。

**Docker企业版 (EE)** 为在大规模生产中构建、装运和运行业务关键应用程序的企业级开发和IT团队设计。查看[Docker Enterprise Edition页面](https://www.docker.com/enterprise-edition) 获取包括采购选项等更多 Docker EE的信息。


### 在CentOS 上安装 Docker CE

你只需要3步即可以在CentOS上安装Docker CE。
企业客户也能在CentOS上安装Docker EE 。

**先决条件：** Docker CE 在 CentOS 7.3 64-bit上被支持。

设置软件仓库
在CentOS上设置Docker CE软件仓库 :

``` shell
sudo yum install -y yum-utils

sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

sudo yum makecache fast
```

在CentOS上 安装最新版的Docker CE：

``` shell
sudo yum -y install docker-ce
```

启动 Docker:

``` shell
sudo systemctl start docker
```

测试你的Docker CE安装


```shell
sudo docker run hello-world
```

已下为这三步的实操记录

#### 设置软件仓库

``` shell
[root@yaoyantest1 ~]# yum install -y yum-utils
已加载插件：fastestmirror, langpacks
base                                                                                                                                                      | 3.6 kB  00:00:00
extras                                                                                                                                                    | 3.4 kB  00:00:00
updates                                                                                                                                                   | 3.4 kB  00:00:00
(1/2): extras/7/x86_64/primary_db                                                                                                                         | 167 kB  00:00:00
(2/2): updates/7/x86_64/primary_db                                                                                                                        | 5.6 MB  00:00:02
Determining fastest mirrors
软件包 yum-utils-1.1.31-40.el7.noarch 已安装并且是最新版本
无须任何处理
[root@yaoyantest1 ~]#
[root@yaoyantest1 ~]# yum-config-manager \
>     --add-repo \
>     https://download.docker.com/linux/centos/docker-ce.repo
已加载插件：fastestmirror, langpacks
adding repo from: https://download.docker.com/linux/centos/docker-ce.repo
grabbing file https://download.docker.com/linux/centos/docker-ce.repo to /etc/yum.repos.d/docker-ce.repo
repo saved to /etc/yum.repos.d/docker-ce.repo
[root@yaoyantest1 ~]#
[root@yaoyantest1 ~]# yum makecache fast
已加载插件：fastestmirror, langpacks
base                                                                                                                                                      | 3.6 kB  00:00:00     
docker-ce-stable                                                                                                                                          | 2.9 kB  00:00:00     
extras                                                                                                                                                    | 3.4 kB  00:00:00     
updates                                                                                                                                                   | 3.4 kB  00:00:00     
docker-ce-stable/x86_64/primary_db                                                                                                                        | 4.8 kB  00:00:00     
Loading mirror speeds from cached hostfile
元数据缓存已建立
```

#### 安装Docker CE并启动

``` shell
[root@yaoyantest1 ~]# yum -y install docker-ce
已加载插件：fastestmirror, langpacks
Loading mirror speeds from cached hostfile
正在解决依赖关系
--> 正在检查事务
---> 软件包 docker-ce.x86_64.0.17.03.1.ce-1.el7.centos 将被 安装
--> 正在处理依赖关系 docker-ce-selinux >= 17.03.1.ce-1.el7.centos，它被软件包 docker-ce-17.03.1.ce-1.el7.centos.x86_64 需要
--> 正在检查事务
---> 软件包 docker-ce-selinux.noarch.0.17.03.1.ce-1.el7.centos 将被 安装
--> 解决依赖关系完成

依赖关系解决

=================================================================================================================================================================================
 Package                                     架构                             版本                                              源                                          大小
=================================================================================================================================================================================
正在安装:
 docker-ce                                   x86_64                           17.03.1.ce-1.el7.centos                           docker-ce-stable                            19 M
为依赖而安装:
 docker-ce-selinux                           noarch                           17.03.1.ce-1.el7.centos                           docker-ce-stable                            28 k

事务概要
=================================================================================================================================================================================
安装  1 软件包 (+1 依赖软件包)

总下载量：19 M
安装大小：19 M
Downloading packages:
警告：/var/cache/yum/x86_64/7/docker-ce-stable/packages/docker-ce-selinux-17.03.1.ce-1.el7.centos.noarch.rpm: 头V4 RSA/SHA512 Signature, 密钥 ID 621e9f35: NOKEY B  --:--:-- ETA
docker-ce-selinux-17.03.1.ce-1.el7.centos.noarch.rpm 的公钥尚未安装
(1/2): docker-ce-selinux-17.03.1.ce-1.el7.centos.noarch.rpm                                                                                               |  28 kB  00:00:00
(2/2): docker-ce-17.03.1.ce-1.el7.centos.x86_64.rpm                                                                                                       |  19 MB  00:00:12
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
总计                                                                                                                                             1.5 MB/s |  19 MB  00:00:12
从 https://download.docker.com/linux/centos/gpg 检索密钥
导入 GPG key 0x621E9F35:
 用户ID     : "Docker Release (CE rpm) <docker@docker.com>"
 指纹       : 060a 61c5 1b55 8a7f 742b 77aa c52f eb6b 621e 9f35
 来自       : https://download.docker.com/linux/centos/gpg
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : docker-ce-selinux-17.03.1.ce-1.el7.centos.noarch                                                                                                             1/2
libsemanage.semanage_direct_install_info: Overriding docker module at lower priority 100 with module at priority 400.
restorecon:  lstat(/var/lib/docker) failed:  No such file or directory
warning: %post(docker-ce-selinux-17.03.1.ce-1.el7.centos.noarch) scriptlet failed, exit status 255
Non-fatal POSTIN scriptlet failure in rpm package docker-ce-selinux-17.03.1.ce-1.el7.centos.noarch
  正在安装    : docker-ce-17.03.1.ce-1.el7.centos.x86_64                                                                                                                     2/2
  验证中      : docker-ce-selinux-17.03.1.ce-1.el7.centos.noarch                                                                                                             1/2
  验证中      : docker-ce-17.03.1.ce-1.el7.centos.x86_64                                                                                                                     2/2

已安装:
  docker-ce.x86_64 0:17.03.1.ce-1.el7.centos

作为依赖被安装:
  docker-ce-selinux.noarch 0:17.03.1.ce-1.el7.centos

完毕！
[root@yaoyantest1 ~]# systemctl start docker
```

#### 测试Dcoker CE安装

``` shell
[root@yaoyantest1 ~]# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
78445dd45222: Pull complete
Digest: sha256:c5515758d4c5e1e838e9cd307f6c6a0d620b5e07e6f927b07d05f6d12a1ac8d7
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://cloud.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/

[root@yaoyantest1 ~]#
```

## 2. Docker运行状态及控制操作

### Docker服务控制

Docker在CentOS中是以服务方式运行，所以可以通过systemctl来查看状态和停启：
  * 查看状态：systemctl status docker
  * 停止： systemctl stop docker
  * 启动： systemctl start docker

``` shell
[root@yaoyantest1 ~]# systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: active (running) since 一 2017-06-05 21:11:39 CST; 10min ago
     Docs: https://docs.docker.com
 Main PID: 14782 (dockerd)
   Memory: 23.7M
   CGroup: /system.slice/docker.service
           ├─14782 /usr/bin/dockerd
           └─14789 docker-containerd -l unix:///var/run/docker/libcontainerd/docker-containerd.sock --metrics-interval=0 --start-timeout 2m --state-dir /var/run/docker/libcon...

6月 05 21:11:39 yaoyantest1 dockerd[14782]: time="2017-06-05T21:11:39.699583549+08:00" level=info msg="Graph migration to content-addressability took 0.00 seconds"
6月 05 21:11:39 yaoyantest1 dockerd[14782]: time="2017-06-05T21:11:39.701165582+08:00" level=info msg="Loading containers: start."
6月 05 21:11:39 yaoyantest1 dockerd[14782]: time="2017-06-05T21:11:39.743228776+08:00" level=info msg="Firewalld running: false"
6月 05 21:11:39 yaoyantest1 dockerd[14782]: time="2017-06-05T21:11:39.871016953+08:00" level=info msg="Default bridge (docker0) is assigned with an IP address 172...IP address"
6月 05 21:11:39 yaoyantest1 dockerd[14782]: time="2017-06-05T21:11:39.932568784+08:00" level=info msg="Loading containers: done."
6月 05 21:11:39 yaoyantest1 dockerd[14782]: time="2017-06-05T21:11:39.948950213+08:00" level=info msg="Daemon has completed initialization"
6月 05 21:11:39 yaoyantest1 dockerd[14782]: time="2017-06-05T21:11:39.949007924+08:00" level=info msg="Docker daemon" commit=c6d412e graphdriver=overlay version=17.03.1-ce
6月 05 21:11:39 yaoyantest1 dockerd[14782]: time="2017-06-05T21:11:39.961141300+08:00" level=info msg="API listen on /var/run/docker.sock"
6月 05 21:11:39 yaoyantest1 systemd[1]: Started Docker Application Container Engine.
6月 05 21:11:46 yaoyantest1 dockerd[14782]: time="2017-06-05T21:11:46.699679677+08:00" level=error msg="Handler for POST /v1.27/containers/create returned error: ...rld:latest"
Hint: Some lines were ellipsized, use -l to show in full.
```

### 确保Docker已经就绪

查看Docker程序是否正常工作： ```docker info```

该命令返回所有容器和镜像（镜像即是Docker用来构建容器的“构建块”）的数量、Docker使用的执行驱动和存储驱动（execution和storage driver），以及Docker的基本配置。效果如下：

```shell
[root@yaoyantest1 ~]# docker info
Containers: 1
 Running: 0
 Paused: 0
 Stopped: 1
Images: 1
Server Version: 17.03.1-ce
Storage Driver: overlay
 Backing Filesystem: extfs
 Supports d_type: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host macvlan null overlay
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: 4ab9917febca54791c5f071a9d1f404867857fcc
runc version: 54296cf40ad8143b62dbcaa1d90e520a2136ddfe
init version: 949e6fa
Security Options:
 seccomp
  Profile: default
Kernel Version: 3.10.0-514.10.2.el7.x86_64
Operating System: CentOS Linux 7 (Core)
OSType: linux
Architecture: x86_64
CPUs: 4
Total Memory: 3.702 GiB
Name: yaoyantest1
ID: I5K6:LPFO:5ULU:S6EA:YA4U:LUPN:QFYK:IDLT:UXVN:J2DE:5ONE:CZ5T
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: https://index.docker.io/v1/
Experimental: false
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false
[root@yaoyantest1 ~]#
```

### Docker命令列表

执行Docker help可以获取所有的docker命令，执行docker COMMAND --help可获得具体某个命令的帮助说明。

```shell
[root@yaoyantest1 ~]# docker help

Usage:    docker COMMAND

A self-sufficient runtime for containers

Options:
      --config string      Location of client config files (default "/root/.docker")
  -D, --debug              Enable debug mode
      --help               Print usage
  -H, --host list          Daemon socket(s) to connect to (default [])
  -l, --log-level string   Set the logging level ("debug", "info", "warn", "error", "fatal") (default "info")
      --tls                Use TLS; implied by --tlsverify
      --tlscacert string   Trust certs signed only by this CA (default "/root/.docker/ca.pem")
      --tlscert string     Path to TLS certificate file (default "/root/.docker/cert.pem")
      --tlskey string      Path to TLS key file (default "/root/.docker/key.pem")
      --tlsverify          Use TLS and verify the remote
  -v, --version            Print version information and quit

Management Commands:
  container   Manage containers
  image       Manage images
  network     Manage networks
  node        Manage Swarm nodes
  plugin      Manage plugins
  secret      Manage Docker secrets
  service     Manage services
  stack       Manage Docker stacks
  swarm       Manage Swarm
  system      Manage Docker
  volume      Manage volumes

Commands:
  attach      Attach to a running container
  build       Build an image from a Dockerfile
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  events      Get real time events from the server
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  history     Show the history of an image
  images      List images
  import      Import the contents from a tarball to create a filesystem image
  info        Display system-wide information
  inspect     Return low-level information on Docker objects
  kill        Kill one or more running containers
  load        Load an image from a tar archive or STDIN
  login       Log in to a Docker registry
  logout      Log out from a Docker registry
  logs        Fetch the logs of a container
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  ps          List containers
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  rmi         Remove one or more images
  run         Run a command in a new container
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  search      Search the Docker Hub for images
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  version     Show the Docker version information
  wait        Block until one or more containers stop, then print their exit codes

Run 'docker COMMAND --help' for more information on a command.
```


## 3. 第一个容器

### 运行我们的第一个容器

执行命令docker run：

```shell
docker run -i -t ubuntu  /bin/bash
```

运行第一个容器参数讲解：
  * 参数   -i  ： 容器打开标准输入 STDIN开启  （Keep STDIN open even if not attached）
  * 参数   -t ： 为容器分配一个伪tty终端 （Allocate a pseudo-TTY）

这样新创建的容器才能提供一个交互式shell。若要在命令行下创建一个我们能与之交互的容器，而不是一个运行后台服务的容器，这两个参数是最基本的参数。

更多docker run 的参数，可参见[官方文档](https://docs.docker.com/engine/reference/commandline/run/)

接下来的是告诉Dcoker 基于什么镜像来创建容器，示例中使用ubuntu镜像，ubuntu镜像是常备镜像，也成为“基础”（base）镜像，它由Docker公司提供，保存在Docker Hub Registry上。可以以ubuntu基础镜像（以及类似的fedora、debian、centos等镜像）为基础，在选择的操作系统上构建自己的镜像。执行上述命令，我们基于此镜像创建了一个容器，并没有对容器增加任何东西。

**背后的执行逻辑：** 首先Docker检查本地是否存在ubuntu镜像，如果本地还没有该镜像的话，Docker默认会连接官方维护的Docker Hub Registry，查看是否有该镜像。Docker找到了该镜像后就会下载镜像到本地宿主机中。
随后，Docker在文件系统内部使用这个镜像创建了一个新容器。该容器拥有自己的网络、ip地址，以及一个用来和宿主机进行通信的桥接网络接口。最后，我们告诉docker在新容器中要运行什么命令，在本例中我们在容器中运行了/bin/bash命令启动一个Bash shell。

当容器创建完毕，Docker就会执行容器中的/bin/bash命令，这时就可以看到容器内的shell了。

```shell
[root@yaoyantest1 ~]# docker run -i -t ubuntu  /bin/bash
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
bd97b43c27e3: Pull complete
6960dc1aba18: Pull complete
2b61829b0db5: Pull complete
1f88dc826b14: Pull complete
73b3859b1e43: Pull complete
Digest: sha256:ea1d854d38be82f54d39efe2c67000bed1b03348bcc2f3dc094f260855dff368
Status: Downloaded newer image for ubuntu:latest
root@ab73f3b47d8d:/#
root@ab73f3b47d8d:/#
```

### 使用第一个容器

现在已经以root身份登录到新容器中，新容器的ID是ab73f3b47d8d。这是一个完整的Ubuntu系统，可以用它来做任何事情。

```shell
root@ab73f3b47d8d:/#
root@ab73f3b47d8d:/# hostname
ab73f3b47d8d
```

查看hostname，容器的主机名就是容器的ID值

```shell
root@ab73f3b47d8d:/#
root@ab73f3b47d8d:/# cat /etc/hosts
127.0.0.1    localhost
::1    localhost ip6-localhost ip6-loopback
fe00::0    ip6-localnet
ff00::0    ip6-mcastprefix
ff02::1    ip6-allnodes
ff02::2    ip6-allrouters
172.17.0.2    ab73f3b47d8d
```

查看容器的/etc/hosts文件，其中已有容器主机的配置项

```shell
root@ab73f3b47d8d:/#
root@ab73f3b47d8d:/# ps -aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0  18240  2048 ?        Ss   14:00   0:00 /bin/bash
root        16  0.0  0.0  34416  1476 ?        R+   14:08   0:00 ps -aux
```

查看容器正在运行的进程，只有一个/bin/bash

```shell
root@ab73f3b47d8d:/# apt-get update
Get:1 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial InRelease [247 kB]
Get:3 http://security.ubuntu.com/ubuntu xenial-security/universe Sources [32.8 kB]
Get:4 http://security.ubuntu.com/ubuntu xenial-security/main amd64 Packages [344 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
Get:6 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
Get:7 http://archive.ubuntu.com/ubuntu xenial/universe Sources [9802 kB]
Get:8 http://security.ubuntu.com/ubuntu xenial-security/restricted amd64 Packages [12.8 kB]
Get:9 http://security.ubuntu.com/ubuntu xenial-security/universe amd64 Packages [154 kB]
Get:10 http://security.ubuntu.com/ubuntu xenial-security/multiverse amd64 Packages [2932 B]
Get:11 http://archive.ubuntu.com/ubuntu xenial/main amd64 Packages [1558 kB]
Get:12 http://archive.ubuntu.com/ubuntu xenial/restricted amd64 Packages [14.1 kB]
Get:13 http://archive.ubuntu.com/ubuntu xenial/universe amd64 Packages [9827 kB]
Get:14 http://archive.ubuntu.com/ubuntu xenial/multiverse amd64 Packages [176 kB]
Get:15 http://archive.ubuntu.com/ubuntu xenial-updates/universe Sources [194 kB]
Get:16 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 Packages [693 kB]
Get:17 http://archive.ubuntu.com/ubuntu xenial-updates/restricted amd64 Packages [13.2 kB]
Get:18 http://archive.ubuntu.com/ubuntu xenial-updates/universe amd64 Packages [596 kB]
Get:19 http://archive.ubuntu.com/ubuntu xenial-updates/multiverse amd64 Packages [9810 B]
Get:20 http://archive.ubuntu.com/ubuntu xenial-backports/main amd64 Packages [4927 B]
Get:21 http://archive.ubuntu.com/ubuntu xenial-backports/universe amd64 Packages [6033 B]
Fetched 24.0 MB in 4min 0s (100.0 kB/s)
Reading package lists... Done
```

更新ubuntu的软件仓库

```shell
root@ab73f3b47d8d:/# apt-get install iproute2
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  libatm1 libmnl0 libxtables11
Suggested packages:
  iproute2-doc
The following NEW packages will be installed:
  iproute2 libatm1 libmnl0 libxtables11
0 upgraded, 4 newly installed, 0 to remove and 0 not upgraded.
Need to get 585 kB of archives.
After this operation, 1808 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 libatm1 amd64 1:2.5.1-1.5 [24.2 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial/main amd64 libmnl0 amd64 1.0.3-5 [12.0 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 iproute2 amd64 4.3.0-1ubuntu3.16.04.1 [522 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial/main amd64 libxtables11 amd64 1.6.0-2ubuntu3 [27.2 kB]
Fetched 585 kB in 2s (262 kB/s)
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package libatm1:amd64.
(Reading database ... 4764 files and directories currently installed.)
Preparing to unpack .../libatm1_1%3a2.5.1-1.5_amd64.deb ...
Unpacking libatm1:amd64 (1:2.5.1-1.5) ...
Selecting previously unselected package libmnl0:amd64.
Preparing to unpack .../libmnl0_1.0.3-5_amd64.deb ...
Unpacking libmnl0:amd64 (1.0.3-5) ...
Selecting previously unselected package iproute2.
Preparing to unpack .../iproute2_4.3.0-1ubuntu3.16.04.1_amd64.deb ...
Unpacking iproute2 (4.3.0-1ubuntu3.16.04.1) ...
Selecting previously unselected package libxtables11:amd64.
Preparing to unpack .../libxtables11_1.6.0-2ubuntu3_amd64.deb ...
Unpacking libxtables11:amd64 (1.6.0-2ubuntu3) ...
Processing triggers for libc-bin (2.23-0ubuntu7) ...
Setting up libatm1:amd64 (1:2.5.1-1.5) ...
Setting up libmnl0:amd64 (1.0.3-5) ...
Setting up iproute2 (4.3.0-1ubuntu3.16.04.1) ...
Setting up libxtables11:amd64 (1.6.0-2ubuntu3) ...
Processing triggers for libc-bin (2.23-0ubuntu7) ...
```

在容器里安装iproute2软件包

```shell
root@ab73f3b47d8d:/# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
8: eth0@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:acff:fe11:2/64 scope link
       valid_lft forever preferred_lft forever
root@ab73f3b47d8d:/#
root@ab73f3b47d8d:/# ip route list
default via 172.17.0.1 dev eth0
172.17.0.0/16 dev eth0  proto kernel  scope link  src 172.17.0.2
```

使用iproute2套件里的ip命令查看网络信息

### 退出容器

用户可以继续在容器中做任何自己想做的事情。当所有工作都结束时，输入exit，就可以返回到容器的宿主机的命令提示符了。

同时，现在容器也停止运行了！ 只有在指定的命令/bin/bash命令处于运行状态时，容器才会相应的处于运行状态，一旦在容器里的bash命令行下exit退出了bash，结束了容器操作，/bin/bash命令也就结束了，容器也随之停止运行。

但容器仍然是存在的。可以使用命令 docker ps -a 命令查看当前容器列表：

```shell
[root@yaoyantest1 ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                         PORTS               NAMES
ab73f3b47d8d        ubuntu              "/bin/bash"         32 minutes ago      Exited (0) 48 seconds ago                          keen_lumiere
737d22fed023        hello-world         "/hello"            About an hour ago   Exited (0) About an hour ago                       sharp_goldwasser
[root@yaoyantest1 ~]#
```

默认情况下，执行docker ps只能看到正在运行的容器，  加上  **-a**  列出所有容器，包括正在运行和已经停止的。

  * **-l** 参数 列出最后一个运行的容器，无论其正在运行还是已经停止
  * 也可以通过 **--format** 标志进一步控制显示哪些信息以及如何显示这些信息。



*<第一天学习结束，学习用书《第一本Docker书》James Turnbull>*




