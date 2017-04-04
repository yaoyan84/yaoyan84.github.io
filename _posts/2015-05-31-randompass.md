---
layout: post
title:  "用python生成随机密码"
date:   2015-05-31 9:46:25
categories: 
tags: python
excerpt: 用python生成随机密码
---

在为一些用户初始化账户时，需要设置初始化密码，想密码是一件很头痛的事情，就写一个小脚本自动生成一些随机字符串当密码。

脚本命名为：randompassword.py

```
#!/usr/bin/env python
import random
import sys
length=int(sys.argv[1])
numbers=int(sys.argv[2])
basechar=['A','B','C','D','E','F','G','I','H','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z','a','b','c','d','e','f','g','i','h','j','k','l','m','n','o','p','q','r','s','t','u','v','w','x','y','z','0','1','2','3','4','5','6','7','8','9']
for i in range(numbers):
    rp = ''
    for j in range(length):
        m = random.randint(0,len(basechar)-1)
        rp = rp + basechar[m]
    print rp
```

使用方法：

```
python randompassword.py 8 10 | tee -a password.txt
```

生成10个8位长度的随机密码，保存在password.txt文件里。

```
etMoXBUd
eY1E5cuH
X9tUpF4Z
Vn9DyiJL
k2dWaQ06
SLQps93a
SHNvr49O
tlTvwxnB
K4zWEqbR
WrUGJHFz
```

为了好辨识，可以避免出现不好识别的字母，比如0oO1l什么的，从basechar里去掉即可。



