---
layout: post
title:  "Docker学习笔记（3）"
date:   2017-06-08 23:06:05
categories: Docker
tags: Docker
excerpt: 学习Docker使用。
---

### 创建守护式容器

除了交互式容器（interactive container）我们还可以创建长期运行的守护式容器（daemonized container）。没有交互式会话，适合运行应用程序和服务。大多时候需要守护式容器。

启动守护式容器：

```
[root@yaoyantest1 ~]# docker run --name daemon_dave -d ubuntu /bin/sh -c "while true; do echo Hello world; sleep 1; done"
503ed13de6554c6c6fca64d49d9f542b1dc817b7a609e529bc128bfa929c3015
```

 * -d 参数，容器将放在后台运行
 * while循环，死循环，一直打印 hello world，直到容器或其进程停止
 * docker run 仅返回一个容器id，并不会附着到容器内的shell会话上

```
[root@yaoyantest1 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
503ed13de655        ubuntu              "/bin/sh -c 'while..."   15 seconds ago      Up 15 seconds                           daemon_dave
```

### 容器内部都在干些什么

docker logs 命令获取容器的日志

```
[root@yaoyantest1 ~]# docker logs daemon_dave
Hello world
Hello world
Hello world
Hello world
Hello world
Hello world
Hello world
Hello world
.......
```

 *  -f 参数来追踪监视日志，类似 tail -f 

```
docker logs -f daemon_dave
```

与tail -f 类似，使用ctrl+C 退出日志追踪监视


 *  --tail 跟踪容器日志片段

```
docker logs --tail 10 daemon_dave   # 日志最后10行
docker logs --tail 0 -f daemon_dave    # 追踪新产生的日志，不读之前的日志
```

 *  -t 参数，为每条日志加上时间戳

```
docker logs -ft daemon_dave
```

与--tail 结合使用

```
[root@yaoyantest1 ~]# docker logs --tail 0 -ft daemon_dave
2017-06-08T13:56:28.576865223Z Hello world
2017-06-08T13:56:29.578051296Z Hello world
2017-06-08T13:56:30.579236051Z Hello world
2017-06-08T13:56:31.580359850Z Hello world
2017-06-08T13:56:32.581514038Z Hello world
2017-06-08T13:56:33.582705786Z Hello world
2017-06-08T13:56:34.583883830Z Hello world
2017-06-08T13:56:35.585212117Z Hello world
2017-06-08T13:56:36.586420836Z Hello world
2017-06-08T13:56:37.587780398Z Hello world
2017-06-08T13:56:38.589040335Z Hello world
2017-06-08T13:56:39.590235300Z Hello world
2017-06-08T13:56:40.591573942Z Hello world
^C
[root@yaoyantest1 ~]#
```

### 日志驱动

Docker 1.6开始，可以控制Docker守护进程和容器所用的日志驱动： 使用 ```--log-driver``` 选项实现

选项有好几个：

 * 默认选项   json-file，之前的```docker logs``` 命令默认就是使用json-file驱动
 *  syslog：该选项将禁用```docker logs```命令，并且将所有容器的日志输出重定向到Syslog，可以在启动Docker守护进程时指定，也可以通过```docker run```对个别容器指定。
 * none： 这个选项将禁用所有容器日志，```docker logs``` 命令也被禁用

新日志驱动随着版本升级会不断增加，1.8中增加了新的日志驱动

```
[root@yaoyantest1 ~]# docker run --log-driver="syslog" --name daemon_dwayne -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
110c383147cc05240a8986015923e1543ee16a3450404c5344419f04537fcd18
```

以上使用了syslog日志驱动，日志写入了主机的/var/log/messages中

```
[root@yaoyantest1 ~]# tail -f /var/log/messages | grep hello
Jun  8 22:17:50 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:17:51 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:17:52 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:17:53 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:17:54 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:17:55 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:17:56 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:17:57 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:17:58 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:17:59 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:18:00 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:18:01 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:18:02 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:18:03 yaoyantest1 110c383147cc[14782]: hello world
Jun  8 22:18:04 yaoyantest1 110c383147cc[14782]: hello world
^C
[root@yaoyantest1 ~]#
```

*<学习用书《第一本Docker书》James Turnbull>*




















