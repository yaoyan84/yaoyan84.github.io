---
layout: post
title:  "Docker学习笔记（2）"
date:   2017-06-07 22:46:05
categories: Docker
tags: Docker
excerpt: 看书学习Docker，第2天。
---

### 容器命名

Docker会为我们创建的每一个容器自动生成一个随机的名字，比如之前创建的容器被命名为 keen_lumiere。
想为容器指定一个名字，而不使用自动生成的名字，可以通过 --name 参数实现：

docker run --name yy_container -i -t ubuntu /bin/bash

创建一个名为 yy_container的容器。
合法容器名：
 * 小写字母a-z
 * 大写字幕A-Z
 * 数字0-9
 * 下划线、圆点、横线

使用正则表达为  **[a-zA-Z0-9_.-]**

容器的名称必须是唯一的，很多命令中可以使用名称来代替ID，更有助于分辨容器。

如果试图创建两个同名的容器，则命令将失败。可先用 ```docker rm``` 命令删除已有的容器，再创建。


### 重启已经停止的容器

之前创建的 yy_container容器退出后就停止了，接下来我们可以重新启动已停止的容器

```
docker start yy_container
```

除了容器名称，我们也可以用容器ID来指定容器

```
docker start 463c36335db5
```

>PS: 也可以使用命令 docker restart 来重新启动一个容器。

此时运行不带-a 参数的 ```docker ps```命令，就可以看到我们的容器已经开始运行了。

```
[root@yaoyantest1 ~]# docker start yy_container
yy_container
[root@yaoyantest1 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
463c36335db5        ubuntu              "/bin/bash"         8 hours ago         Up 5 seconds                            yy_container
[root@yaoyantest1 ~]#
```

>NOTE: Docker也提供了```docker create```命令来创建一个容器，但并不运行它。之前用```docker run```创建并直接运行容器，根据需要选择。

### 附着到容器上

Docker容器重新启动时，会沿用```docker run```命令时指定的参数来运行，因此我们的容器重新启动后，在内部会运行一个交互式会话shell。我们可以使用 ```docker attach```命令，重新附着到该容器的会话上。
即重新进入容器内部，打开容器内部的shell会话的控制台。

```
[root@yaoyantest1 ~]# docker attach yy_container
root@463c36335db5:/#
root@463c36335db5:/# hostname
463c36335db5
root@463c36335db5:/# cat /etc/hosts
127.0.0.1    localhost
::1    localhost ip6-localhost ip6-loopback
fe00::0    ip6-localnet
ff00::0    ip6-mcastprefix
ff02::1    ip6-allnodes
ff02::2    ip6-allrouters
172.17.0.2    463c36335db5
```

如果执行exit退出shell会话，则容器会再次立即停止运行。

*<学习用书《第一本Docker书》James Turnbull>*