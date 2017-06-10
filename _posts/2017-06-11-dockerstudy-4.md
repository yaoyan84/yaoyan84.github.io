---
layout: post
title:  "Docker学习笔记（4）"
date:   2017-06-11 00:49:05
categories: Docker
tags: Docker
excerpt: Docker学习。
---


### 查看容器内的进程

docker top命令

```
[root@yaoyantest1 etc]# docker top daemon_dave
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                1667                1652                0                   00:04               ?                   00:00:00            /bin/sh -c while true; do echo Hello world; sleep 1; done
root                1873                1667                0                   00:07               ?                   00:00:00            sleep 1
```

### Docker 统计信息

```docker stats``` 命令，可同时查看多个容器的状态

```
[root@yaoyantest1 etc]# docker stats daemon_dave daemon_dwayne
CONTAINER           CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
daemon_dave         0.04%               184 KiB / 1.938 GiB   0.01%               1.3 kB / 648 B      0 B / 0 B           2
daemon_dwayne       0.04%               188 KiB / 1.938 GiB   0.01%               648 B / 648 B       0 B / 0 B           2
```

```docker stats``` 输出类似在shell中执行top，会打开一个持续定时刷新的监控页面，监控所指定的容器状态

### 在容器内部运行进程

```docker exec```  命令，在容器中额外启动新的进程。在容器内可以运行的进程有两种：后台任务 和 交互式任务（打开shell进行交互）。

* 在容器内运行后台任务：

```
[root@yaoyantest1 etc]# docker exec -d daemon_dave touch /etc/new_config_file
[root@yaoyantest1 etc]#
```

* 在容器内运行交互任务：

```
[root@yaoyantest1 etc]# docker exec -t -i daemon_dave /bin/bash
root@a4650790fa34:/#
root@a4650790fa34:/#
root@a4650790fa34:/# cd /etc
root@a4650790fa34:/etc# ll | grep new_config
-rw-r--r-- 1 root root       0 Jun 10 16:14 new_config_file
root@a4650790fa34:/etc# date
Sat Jun 10 16:15:43 UTC 2017
root@a4650790fa34:/etc#
```

和运行交互式容器一样， -i  和 -t 标志创建tty 并捕获STDIN。

### 停止守护式容器

```docker stop```命令

```
[root@yaoyantest1 etc]# docker stop daemon_dwayne
daemon_dwayne
```

### 自动重启容器

* --restart   标志，让Docker自动重启该容器，默认不重启

```
docker run --restart=always  --name daemon_always -d ubuntu /bin/sh -c "while true; do echo Hello world; sleep 1; done"
```

 * --restart=always           永远自动重启，不管退出代码
 * --restart=on-failure      退出代码非0，异常停止时重启
 * --restart=on-failure:5   退出代码非0时，重启容器，最多重启5次。


### 深入容器

除了通过docker ps命令获取容器信息，还可以使用 ```docker inspect``` 命令

```
[root@yaoyantest1 etc]# docker inspect daemon_always
[
    {
        "Id": "aa6f4ec363b2d0395f1d884b96fcfe1af99a48ea7bd41557315bec6d09ab777a",
        "Created": "2017-06-10T16:22:47.677631323Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true; do echo Hello world; sleep 1; done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 3757,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2017-06-10T16:22:47.811859301Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:7b9b13f7b9c086adfb6be4d2d264f90f16b4d1d5b3ab9f955caa728c3675c8a2",
        "ResolvConfPath": "/var/lib/docker/containers/aa6f4ec363b2d0395f1d884b96fcfe1af99a48ea7bd41557315bec6d09ab777a/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/aa6f4ec363b2d0395f1d884b96fcfe1af99a48ea7bd41557315bec6d09ab777a/hostname",
        "HostsPath": "/var/lib/docker/containers/aa6f4ec363b2d0395f1d884b96fcfe1af99a48ea7bd41557315bec6d09ab777a/hosts",
        "LogPath": "/var/lib/docker/containers/aa6f4ec363b2d0395f1d884b96fcfe1af99a48ea7bd41557315bec6d09ab777a/aa6f4ec363b2d0395f1d884b96fcfe1af99a48ea7bd41557315bec6d09ab777a-json.log",
        "Name": "/daemon_always",
        "RestartCount": 0,
        "Driver": "overlay",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": [
            "f76ead4083f2914c802a7e328cbf35602e46b2c2430de179b1567d52973f0f21",
            "e4ecd77b8d6dad7429c5dd05e8ef6105cecb54f5f865494a3b5d44a713562c29",
            "aceed8518935ee5ac910691d27e7a3394fef236b664e5b12a3b57fd479c7186b"
        ],
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "always",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": null,
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DiskQuota": 0,
            "KernelMemory": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": -1,
            "OomKillDisable": false,
            "PidsLimit": 0,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0
        },
        "GraphDriver": {
            "Name": "overlay",
            "Data": {
                "LowerDir": "/var/lib/docker/overlay/20d678c1e7fa2c5170b5dba00494e3c3d37a982a080db9803e0dd18102db1a36/root",
                "MergedDir": "/var/lib/docker/overlay/aa1ddf886a4061bd4987246591f69c3e0de5ac17a81c84bf4ea7de09195e201b/merged",
                "UpperDir": "/var/lib/docker/overlay/aa1ddf886a4061bd4987246591f69c3e0de5ac17a81c84bf4ea7de09195e201b/upper",
                "WorkDir": "/var/lib/docker/overlay/aa1ddf886a4061bd4987246591f69c3e0de5ac17a81c84bf4ea7de09195e201b/work"
            }
        },
        "Mounts": [],
        "Config": {
            "Hostname": "aa6f4ec363b2",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true; do echo Hello world; sleep 1; done"
            ],
            "Image": "ubuntu",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {}
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "ce10f39e5f9c5d95e02a13ca1df1e052b348a5d45367cc3740e0afd9c84e6fbf",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/ce10f39e5f9c",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "7ca857a7b08d1e45dacfc7da037f7eea8c6c41048ba6c55936213c3ea8cb26ac",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.3",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:03",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "874555d6733a1f217f3b3357196477d32d780d33ba1f0fe1937ff6b3268ef919",
                    "EndpointID": "7ca857a7b08d1e45dacfc7da037f7eea8c6c41048ba6c55936213c3ea8cb26ac",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.3",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:03"
                }
            }
        }
    }
]
[root@yaoyantest1 etc]#
```

```docker inspect``` 返回容器的详细配置信息

 * -f  或  --format 选定查看结果，支持完整的Go语言模板

```docker inspect``` 可同时查看多个容器

>NOTE： 可以通过浏览 /var/lib/docker 目录深入了解Docker的工作原理。该目录存放着Docker的镜像、容器以及容器的配置。所有的容器都保存在/var/lib/docker/containers 目录下。

### 删除容器

* docker rm 命令删除容器

```
[root@yaoyantest1 etc]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                        PORTS               NAMES
aa6f4ec363b2        ubuntu              "/bin/sh -c 'while..."   18 minutes ago      Up 18 minutes                                     daemon_always
0ff9cb342acc        ubuntu              "/bin/sh -c 'while..."   32 minutes ago      Exited (137) 21 minutes ago                       daemon_dwayne
a4650790fa34        ubuntu              "/bin/sh -c 'while..."   36 minutes ago      Up 36 minutes                                     daemon_dave
42a796e3602f        hello-world         "/hello"                 44 minutes ago      Exited (0) 44 minutes ago                         practical_spence
[root@yaoyantest1 etc]#
[root@yaoyantest1 etc]#
[root@yaoyantest1 etc]# docker rm daemon_dwayne
daemon_dwayne
[root@yaoyantest1 etc]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
aa6f4ec363b2        ubuntu              "/bin/sh -c 'while..."   18 minutes ago      Up 18 minutes                                   daemon_always
a4650790fa34        ubuntu              "/bin/sh -c 'while..."   37 minutes ago      Up 37 minutes                                   daemon_dave
42a796e3602f        hello-world         "/hello"                 45 minutes ago      Exited (0) 45 minutes ago                       practical_spence
[root@yaoyantest1 etc]#
```

 * docker rm -f  强制删除正在运行中的容器（1.6.2开始支持）

```
[root@yaoyantest1 etc]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
aa6f4ec363b2        ubuntu              "/bin/sh -c 'while..."   18 minutes ago      Up 18 minutes                                   daemon_always
a4650790fa34        ubuntu              "/bin/sh -c 'while..."   37 minutes ago      Up 37 minutes                                   daemon_dave
42a796e3602f        hello-world         "/hello"                 45 minutes ago      Exited (0) 45 minutes ago                       practical_spence
[root@yaoyantest1 etc]# docker rm -f daemon_always
daemon_always
[root@yaoyantest1 etc]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
a4650790fa34        ubuntu              "/bin/sh -c 'while..."   38 minutes ago      Up 38 minutes                                   daemon_dave
42a796e3602f        hello-world         "/hello"                 46 minutes ago      Exited (0) 46 minutes ago                       practical_spence
[root@yaoyantest1 etc]#
```

 * 删除所有容器：   docker rm -f  $(docker ps -a -q)

```
[root@yaoyantest1 etc]# docker rm -f  $(docker ps -a -q)
a4650790fa34
42a796e3602f
[root@yaoyantest1 etc]#
[root@yaoyantest1 etc]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@yaoyantest1 etc]#
```

```docker ps``` 命令  -a 标志 列出所有容器，  -q 标志 只返回容器ID不返回其他信息。将容器ID列表传给  ```docker rm -f```  命令处理，所以所有容器被删除。


*<学习用书《第一本Docker书》James Turnbull>*

