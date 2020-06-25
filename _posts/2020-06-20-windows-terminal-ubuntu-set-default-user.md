---
layout: post
title:  "Win10 的Linux子系统默认用户修改"
date:   2020-06-20 14:21:11
categories: 
tags:  Win10
excerpt: 
---

重装系统后，安装Linux子系统还未创建过用户，使用Windows Terminal打开时，默认就进到root，所以需要修改默认的用户。

安装的Linux子系统是windows 商店里的ubuntu，所以使用如下命令可以看到帮助信息：

```
PS C:\Users\yycu> ubuntu /?
```

输出：

```
Usage:
    <no args>
        Launches the user's default shell in the user's home directory.

    install [--root]
        Install the distribuiton and do not launch the shell when complete.
          --root
              Do not create a user account and leave the default user set to root.

    run <command line>
        Run the provided command line in the current working directory. If no
        command line is provided, the default shell is launched.

    config [setting [value]]
        Configure settings for this distribution.
        Settings:
          --default-user <username>
              Sets the default user to <username>. This must be an existing user.

    help
        Print usage information.
```

由上可知，要设置ubuntu的默认用户，语句为：

```powershell
ubuntu config  --default-user <username>
```

且该用户必须已经存在。使用root在linux子系统中创建新组和用户：

```bash
 groupadd -g 1000 usr
 useradd -g usr -s /bin/bash -G admin,sudo -m yaoyan
```

然后在PowerShell中执行：

```powershell
ubuntu config  --default-user yaoyan
```

然后再打开Linux子系统时，直接使用的新建的用户进入。
