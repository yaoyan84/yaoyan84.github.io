---
layout: post
title:  "CentOS8下使用rpm包安装docker环境"
date:   2020-05-23 19:21:12
categories: 
tags:  docker
excerpt: 
---

## 1、获取RPM包

```
wget https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.13-3.2.el7.x86_64.rpm
wget https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-19.03.9-3.el7.x86_64.rpm
wget https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-cli-19.03.9-3.el7.x86_64.rpm
```

### 2、卸载 `podman-manpages`

解决安装时报`Error: Transaction check error:` 错误，提示 `docker-ce-cli` 与`podman-manpages` 有文件冲突的问题，需要卸载`podman-manpages` 。

```
yum remove podman-manpages.noarch 
```

### 3、安装

```
yum install containerd.io-1.2.13-3.2.el7.x86_64.rpm docker-ce-19.03.9-3.el7.x86_64.rpm docker-ce-cli-19.03.9-3.el7.x86_64.rpm
```

### 4、启动docker守护

```
systemctl enable docker
systemctl start docker
```

### 5、查看状态

```
systemctl status docker
```

![image-20200523151439937](/images/posts/2020/image-20200523151439937.png)
