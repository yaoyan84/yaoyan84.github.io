---
layout: post
title:  "关闭SELinux"
date:   2020-11-07 11:21:12
categories: 
tags: Linux
excerpt: 
---

## 查看SELinux 状态

### 方法1

```bash
 /usr/sbin/sestatus -v
```

如：

```
[root@localhost ~]# /usr/sbin/sestatus -v
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      31

Process contexts:
Current context:                unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
Init context:                   system_u:system_r:init_t:s0
/usr/sbin/sshd                  system_u:system_r:sshd_t:s0-s0:c0.c1023

File contexts:
Controlling terminal:           unconfined_u:object_r:user_devpts_t:s0
/etc/passwd                     system_u:object_r:passwd_file_t:s0
/etc/shadow                     system_u:object_r:shadow_t:s0
/bin/bash                       system_u:object_r:shell_exec_t:s0
/bin/login                      system_u:object_r:login_exec_t:s0
/bin/sh                         system_u:object_r:bin_t:s0 -> system_u:object_r:shell_exec_t:s0
/sbin/agetty                    system_u:object_r:getty_exec_t:s0
/sbin/init                      system_u:object_r:bin_t:s0 -> system_u:object_r:init_exec_t:s0
/usr/sbin/sshd                  system_u:object_r:sshd_exec_t:s0
```

### 方法2

```
getenforce
```

如：

```
[root@localhost ~]# getenforce
Enforcing
```

## 临时关闭SELinux

```
setenforce 0
```

查看结果：

```
[root@localhost ~]# getenforce
Permissive
```

进入宽容模式，只记录，不限制。



## 永久关闭SELinux

修改配置文件 /etc/selinux/config，将```SELINUX=enforcing ```行改为 ```SELINUX=disabled```

```
sed -i '/SELINUX/s/enforcing/disabled/' /etc/selinux/config  
```

修改完成侯，需要重启生效。

## 确认状态

```
[root@localhost ~]# /usr/sbin/sestatus -v
SELinux status:                 disabled
[root@localhost ~]# getenforce
Disabled
[root@localhost ~]#
```
