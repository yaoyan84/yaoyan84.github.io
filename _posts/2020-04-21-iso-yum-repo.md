---
layout: post
title:  "使用ISO文件做yum源"
date:   2020-04-21 19:11:25
categories: 
tags:  linux yum
excerpt: 
---

安装服务器后，远程挂ISO安装软件包比较慢，将ISO拷贝的服务器上做本地源。

mount ISO

```bash
mkdir -p /mnt/iso
mount -t iso9660 -o loop /path/to/xxxx.iso /mnt/iso
```

清空 `/etc/yum.repos.d/` 内的文件，新建 iso.repo 文件，内容：

```ini
[iso]
name=iso
baseurl=file:///mnt/cdrom
enable=1
gpgcheck=0
```

然后

```bash 
yum makecache 
```



