---
layout: post
title:  "Docker学习笔记（5）"
date:   2017-06-12 22:34:05
categories: Docker
tags: Docker
excerpt: Docker学习。
---


## 使用Docker镜像和仓库

### 什么是Docker镜像

Docker镜像是由文件系统叠加而成。

最底端是一个引导文件系统bootfs，第二层是rootfs，但与linux传统root文件系统不同，采用**联合加载技术（union mount）**，一次加载多个只读文件系统，但外界只看到一个，是各层依次叠加之后的最终效果。Docker称这样的文件系统为镜像。

一个镜像可以放在另一个镜像的顶部。位于下面的镜像称为父镜像（parent image），依此类推到镜像栈的底部，最底部为基础镜像（base image）。当从镜像启动一个容器时，Docker会在该镜像的最顶层再加一个 可读写的文件系统。我们在Docker容器中运行的程序和所做的操作，都是再读写层中执行。


当Docker容器第一次启动时，初始的读写层时空的，当文件系统发生了变化时，这些变化会应用到这一层上。

**写时复制（copy on write）机制**：当修改写入一个只读层中已有的文件时，文件从只读层复制到读写层，修改后的版本存在读写层，只读层的版本依然存在，只是因为读写层再最顶层，所以该文件只读层的版本被隐藏了。

### 列出镜像


 * docker images

```
[root@yaoyantest1 ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              7b9b13f7b9c0        8 days ago          118 MB
hello-world         latest              48b5124b2768        4 months ago        1.84 kB
```

本地镜像都保存在Docker 宿主机 /var/lib/docker 目录下。

### Docker宿主机上的目录探索


 * 每个镜像都保存在Docker所采用的存储驱动目录下面，如aufs或者devicemapper。

```
[root@yaoyantest1 docker]# pwd
/var/lib/docker
[root@yaoyantest1 docker]# ll
总用量 4
drwx------  4 root root  150 6月  11 16:04 containers
drwx------  3 root root   21 6月  10 23:55 image
drwxr-x---  3 root root   19 6月  10 23:55 network
drwx------ 12 root root 4096 6月  11 16:04 overlay
drwx------  4 root root   32 6月  10 23:55 plugins
drwx------  2 root root    6 6月  10 23:55 swa
drwx------  2 root root    6 6月  11 00:04 tmp
drwx------  2 root root    6 6月  10 23:55 trust
drwx------  2 root root   25 6月  10 23:55 volumes
```

经过观察，发现docker目录里的结构与以上书中表述有些不同。目前所用版本时Docker 17.03。

其中的 containers目录中仍然时保存各容器信息：

 * containers 目录容器信息，以全长64位的ID为目录名

```
[root@yaoyantest1 containers]# pwd
/var/lib/docker/containers
[root@yaoyantest1 containers]# ll
总用量 0
drwx------ 4 root root 234 6月  11 16:04 094fb0e80ea84e52b04eb2fe8045cb3833c0272c23baeb4ea185a9da330ba01d
drwx------ 4 root root 234 6月  11 16:03 1d1d91f8856040070038665dc4a97c22de6803b3565edf3caea63d5b4a00965f
[root@yaoyantest1 containers]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
094fb0e80ea8        ubuntu              "/bin/sh -c 'while..."   58 seconds ago      Up 57 seconds                                  daemon_test
1d1d91f88560        hello-world         "/hello"                 2 minutes ago       Exited (0) 2 minutes ago                       condescending_morse
```

 * image目录保存镜像信息：保存镜像的配置信息、文件系统信息，比如都由哪些文件系统层叠加而成，容器镜像及文件挂载信息（/var/lib/docker/image/overlay/layerdb/mounts）等。目录名称涉及文件系统层、镜像等信息时同样适用64位的ID值做目录名；
 * overlay 目录中保存各层文件系统具体内容，文件数据等信息，包含保存构建镜像的只读层和容器读写层。如在容器中创建一个文件，在该目录下对应的文件系统层中会保存有该文件的数据。

```
[root@yaoyantest1 docker]# du -sm *
1       containers
1       image
1       network
125     overlay
0       plugins
0       swarm
0       tmp
0       trust
1       volumes
```

可以看出overlay占用较大，各层文件系统的实际数据都保存在里面。

```
[root@yaoyantest1 docker]# cd overlay/
[root@yaoyantest1 overlay]# ll
总用量 0
drwx------ 3 root root 18 6月  11 00:04 20d678c1e7fa2c5170b5dba00494e3c3d37a982a080db9803e0dd18102db1a36
drwx------ 3 root root 18 6月  11 00:04 73ad0e513b653cfc1429e71bcede99a1c3cb676fa4eb8a4bc36160016980d9c2
drwx------ 3 root root 18 6月  11 00:04 b53d5efadf56d20af076dd54741f2e3f719dc49ae6bd65d671d64273140d61c2
drwx------ 5 root root 61 6月  11 16:04 bc4ac4fb5e2c0b1c561e7d8486bca81a707186889546e0b82f78bc5e0d9de538
drwx------ 5 root root 61 6月  11 16:04 bc4ac4fb5e2c0b1c561e7d8486bca81a707186889546e0b82f78bc5e0d9de538-init
drwx------ 3 root root 18 6月  11 00:04 cbd6d6edebd0b6a2935c1a3478c5c334193a9f1349700286d05bdf6d1a192c15
drwx------ 3 root root 18 6月  10 23:56 f6e818da55a8c06cabb1ed7b2f8ca144d56fb68b254231489fb74919d3309435
drwx------ 3 root root 18 6月  11 00:04 f921eb1de427f91176b61a24145e5efcfe32746bc29ff976e4383b4d9f767698
drwx------ 5 root root 61 6月  11 16:03 fffc469e562c425d0b80a90bbb20e0254f57ac5b49be946147da3b1e537f6055
drwx------ 5 root root 61 6月  11 16:03 fffc469e562c425d0b80a90bbb20e0254f57ac5b49be946147da3b1e537f6055-init

[root@yaoyantest1 docker]# docker exec -i -t 094fb0e80 /bin/bash
root@094fb0e80ea8:/# mkdir testdir
root@094fb0e80ea8:/# cd testdir/
root@094fb0e80ea8:/testdir# echo "test! test!" >testfile
root@094fb0e80ea8:/testdir# cat testfile
test! test!
root@094fb0e80ea8:/testdir# exit
exit
[root@yaoyantest1 docker]# find ./ -name testfile
./overlay/bc4ac4fb5e2c0b1c561e7d8486bca81a707186889546e0b82f78bc5e0d9de538/upper/testdir/testfile
./overlay/bc4ac4fb5e2c0b1c561e7d8486bca81a707186889546e0b82f78bc5e0d9de538/merged/testdir/testfile
```

以上发现，在容器中创建了文件testfile，在 bc4ac4fb5e2c0b1c561e7d8486bca81a707186889546e0b82f78bc5e0d9de538 文件系统层的文件夹中找到了两个文件。

```
[root@yaoyantest1 bc4ac4fb5e2c0b1c561e7d8486bca81a707186889546e0b82f78bc5e0d9de538]# ll
总用量 4
-rw-r--r-- 1 root root 64 6月  11 16:04 lower-id
drwxr-xr-x 1 root root 73 6月  11 16:28 merged
drwxr-xr-x 6 root root 73 6月  11 16:28 upper
drwx------ 3 root root 18 6月  11 16:04 work
```

这是容器 094fb0e80ea8 的 顶层读写文件系统层，在该层的数据目录中：

 * merged 是融合目录，即融合了该容器所有层之后的最终结果，与我们在容器中看到的文件系统的最终效果一致
 * upper 中是该层本来的数据，即读写层中的本来的文件，在容器中修改下层已存在的文件，就会通过写时复制拷贝在此处后再保存，再容器中新创建的文件也保存在此处。

如果是镜像的文件系统层，是只读层，则其中只有一个root文件夹，即rootfs

```
[root@yaoyantest1 overlay]# cd 73ad0e513b653cfc1429e71bcede99a1c3cb676fa4eb8a4bc36160016980d9c2/
[root@yaoyantest1 73ad0e513b653cfc1429e71bcede99a1c3cb676fa4eb8a4bc36160016980d9c2]# ll
总用量 0
drwxr-xr-x 21 root root 224 6月  11 00:04 root
```

### Docker镜像仓库

镜像从仓库下载下来。镜像保存在仓库中，而仓库存在于Registry中。默认的Registry是由Docker公司运营的公共Registry服务，即Docker Hub。
国内有阿里云的Docker Registry镜像。

Docker Registry代码是开源的，任何人都可以运行自己的私有Registry，

可以将镜像仓库像想为类似Git仓库的东西，镜像保存在仓库中，包括镜像、层以及关于镜像的元数据。

每个镜像仓库都可以存放很多镜像，比如ubuntu仓库，我们还可以获取一个别的镜像


 * docker pull ubuntu:12.04

拉取ubuntu仓库的Ubuntu 12.04镜像到本地。

```
[root@yaoyantest1 docker]# docker pull ubuntu:12.04
12.04: Pulling from library/ubuntu
d8868e50ac4c: Pull complete
83251ac64627: Pull complete
589bba2f1b36: Pull complete
d62ecaceda39: Pull complete
6d93b41cfc6b: Pull complete
Digest: sha256:18305429afa14ea462f810146ba44d4363ae76e4c8dfc38288cf73aa07485005
Status: Downloaded newer image for ubuntu:12.04
[root@yaoyantest1 docker]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              7b9b13f7b9c0        8 days ago          118 MB
ubuntu              12.04               5b117edd0b76        8 weeks ago         104 MB
hello-world         latest              48b5124b2768        4 months ago        1.84 kB
[root@yaoyantest1 docker]#
```

我们已得到了ubuntu仓库的两个latest和12.04两个镜像。这表明，ubuntu镜像是聚集在一个仓库中的一系列镜像。TAG用来区分同一个仓库的不同镜像。

Docker Hub中有两种类型的仓库： **用户仓库（user repository）** 和 **顶层仓库（top-level repository）**

 * 用户镜像有社区的用户创建和维护，仓库名包含用户名：  username/repname
 * 顶层仓库由Docker公司和选定的基础镜像厂商（官方）提供: 类似ubuntu，直接使用仓库名


### 拉取镜像

 * docker pull

默认拉取仓库中的 latest 镜像。

```
[root@yaoyantest1 docker]# docker pull jamtur01/puppetmaster
Using default tag: latest
latest: Pulling from jamtur01/puppetmaster
a3ed95caeb02: Pull complete
6e71c809542e: Pull complete
d196a7609355: Pull complete
08f6dff5acea: Pull complete
ce65532003d0: Pull complete
d1f76d0972bd: Pull complete
c9ed023cc2c9: Pull complete
Digest: sha256:2eedcf4cbe50f7052b420b2b125969873872d76ce36752f04f5160160acd1258
Status: Downloaded newer image for jamtur01/puppetmaster:latest
[root@yaoyantest1 docker]#  docker run -i -t --name testpuppet jamtur01/puppetmaster /bin/bash
root@ab4dc66d06ad:/#
root@ab4dc66d06ad:/#
root@ab4dc66d06ad:/# facter
architecture => amd64
augeasversion => 1.2.0
blockdevice_sda_model => VMware Virtual S
blockdevice_sda_size => 53687091200
blockdevice_sda_vendor => VMware,
.......
root@ab4dc66d06ad:/# puppet --version
3.4.3
```

### 查找镜像

 * docker search

```
[root@yaoyantest1 docker]# docker search fedora
NAME                         DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
fedora                       Official Docker builds of Fedora                544       [OK]
mattsch/fedora-nzbhydra      Fedora NZBHydra                                 4                    [OK]
startx/fedora                Simple container used for all startx based...   3                    [OK]
dockingbay/fedora-rust       Trusted build of Rust programming language...   3                    [OK]
gluster/gluster-fedora       Official GlusterFS image [ Fedora ( latest...   3                    [OK]
darksheer/fedora22           Base Fedora 22 Image -- Updated hourly          1                    [OK]
darksheer/fedora25           Hourly updated Fedora 24 Docker Hub Image       1                    [OK]
darksheer/fedora24           Hourly update Fedora 24                         1                    [OK]
darksheer/fedora             Hourly update latest Fedora Image               1                    [OK]
pacur/fedora-22              Pacur Fedora 22                                 1                    [OK]
darksheer/fedora23           Hourly updated Fedora 23                        1                    [OK]
mattsch/fedora-rpmfusion     Base container for Fedora with RPM Fusion ...   1                    [OK]
vcatechnology/fedora         A Fedora image that is updated daily            0                    [OK]
mattsch/fedora-sonarr        Fedora Sonarr                                   0                    [OK]
krystalcode/fedora           Fedora base image that includes some addit...   0                    [OK]
heichblatt/fedora-pkgsrc     pkgsrc on latest Fedora                         0                    [OK]
mattsch/fedora-plexpy        Fedora PlexPy Container                         0                    [OK]
termbox/fedora               Fedora                                          0                    [OK]
smartentry/fedora            fedora with smartentry                          0                    [OK]
mattsch/fedora-nzbget        Fedora NZBGet                                   0                    [OK]
vcatechnology/fedora-ci      A Fedora image that is used in the VCA Tec...   0                    [OK]
mattsch/fedora-couchpotato   Fedora Couchpotato                              0                    [OK]
zartsoft/fedora              Base fedora image                               0                    [OK]
qnib/fedora                  Base QNIBTerminal image of fedora               0                    [OK]
opencpu/fedora               OpenCPU stable release for Fedora (via OBS)     0                    [OK]
```

返回信息：

 * NAME 仓库名
 * DESCRIPTION 描述
 * STARS 用户评价，反映出一个镜像受欢迎的程度
 * OFFICIAL 是否是官方镜像
 * AUTOMATED 自动构建，镜像是由Docker Hub的自动构建流程创建的

### 构建镜像

构建镜像有两种方法： **使用 docker commit 命令** 和 **使用docker build命令和Dockerfile文件**

不推荐使用```docker commit```命令构建镜像，Docker推荐的方法是编写Dockerfile 之后使用 docker build命令构建镜像。

NOTE： 一般的我们不会真正的“创建” 新镜像，而是基于一个已有的基础镜像，如ubuntu镜像或fedora镜像构建新的镜像而已。

#### 注册Docker Hub账号

构建镜像中很重要的一环就是如何共享和发布镜像。可以将镜像推送到Docker Hub或者用户自己的私有Registry中。

注册Docker Hub账号：https://hub.docker.com/


测试注册的账号是否可以正常登陆，使用：```docker login``` 命令

```
[root@yaoyantest1 docker]# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: yaoyan84
Password:
Login Succeeded
[root@yaoyantest1 docker]#
```

登陆后，认证信息将被保存起来以供知乎使用。使用```docker logout```命令可以退出一个Registry服务器。

>NOTE: 用户的个人信息保存在 $HOME/.docker/config.json 中。

#### 使用 docker commit 命令创建镜像

创建Docker镜像的第一种方式是使用```docker commit```命令。可以将此想象为我们在往版本控制系统里提交变更。我们创建一个容器，并在容器里做出修改，就像修改代码一样，最后再将修改提交为一个新的镜像。

```
[root@yaoyantest1 ~]# docker run -i -t ubuntu /bin/bash
root@2ecd8377c3a2:/#
root@2ecd8377c3a2:/# apt-get -y update
Get:2 http://archive.ubuntu.com/ubuntu xenial InRelease [247 kB]
Get:1 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
Get:4 http://security.ubuntu.com/ubuntu xenial-security/universe Sources [35.1 kB]
Get:5 http://security.ubuntu.com/ubuntu xenial-security/main amd64 Packages [354 kB]
Get:6 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
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
Fetched 23.7 MB in 3min 3s (129 kB/s)
Reading package lists... Done
```

运行一个容器，作为定制的基础，将容器内的ubuntu软件源更新

```
root@2ecd8377c3a2:/# apt-get -y install vim
......
root@2ecd8377c3a2:/# apt-get -y install nginx
......
root@2ecd8377c3a2:~# exit
exit
[root@yaoyantest1 ~]#
```

在默认的ubuntu镜像运行的容器里，安装了vim和nginx。 exit退出后，提交镜像 yaoyan84/nginx，并列出镜像：

```
[root@yaoyantest1 ~]# docker commit 2ecd8377c3a2 yaoyan84/nginx
sha256:99d917d65caf59986535e11a3b997e22597c5bcb42f55b2f1dc0375078f4c368
[root@yaoyantest1 ~]# docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
yaoyan84/nginx          latest              99d917d65caf        45 seconds ago      266 MB
ubuntu                  latest              7b9b13f7b9c0        8 days ago          118 MB
ubuntu                  12.04               5b117edd0b76        8 weeks ago         104 MB
hello-world             latest              48b5124b2768        4 months ago        1.84 kB
jamtur01/puppetmaster   latest              ada3e5bfc468        3 years ago         312 MB
```

docker commit 支持参数，用以描述提交的内容

```
[root@yaoyantest1 ~]# docker commit -m "new custom image, ubuntu with vim and nginx" -a " Yao Yan" 2ecd8377c3a2 yaoyan84/nginx:webproxy
sha256:c12d0aedecfd1b3b06bf89d3bd3ca787eece51ab691a3584896b6302d7366d65
```

 * -m 提交信息
 * -a 镜像作者
 * 并且为镜像增加了标签 webproxy

```
[root@yaoyantest1 ~]# docker images
REPOSITORY              TAG                 IMAGE ID            CREATED              SIZE
yaoyan84/nginx          webproxy            c12d0aedecfd        About a minute ago   266 MB
yaoyan84/nginx          latest              99d917d65caf        6 minutes ago        266 MB
ubuntu                  latest              7b9b13f7b9c0        8 days ago           118 MB
ubuntu                  12.04               5b117edd0b76        8 weeks ago          104 MB
hello-world             latest              48b5124b2768        4 months ago         1.84 kB
jamtur01/puppetmaster   latest              ada3e5bfc468        3 years ago          312 MB
[root@yaoyantest1 ~]#
```

执行 ```docker inspect yaoyan84/nginx:webproxy```  可查看镜像详细信息。

>NOTE： https://docs.docker.com/engine/reference/commandline/commit/ 可查看全部选项

**从提交的镜像创建容器**

```
[root@yaoyantest1 ~]# docker run -i -t yaoyan84/nginx:webproxy /bin/bash
root@caf6c2c2f48a:/# ll
root@caf6c2c2f48a:/# which nginx
/usr/sbin/nginx
```

*<学习用书《第一本Docker书》James Turnbull>*
