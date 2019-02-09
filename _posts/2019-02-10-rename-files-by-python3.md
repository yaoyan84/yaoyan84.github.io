---
layout: post
title:  "使用python3批量修改文件名"
date:   2019-02-09 20:00:05
categories: python3
tags:  python3
excerpt: "使用python3自带的os模块，批量修改文件名"
---

最近在下载视频时，偷懒在编号后直接写标题名字，中间没有用分隔符：

```
001视频001名字随便.mp4
002视频002名字随便.mp4
...
...
```

导致后来标题有数字开头，产生了混淆，排序也不对了，所以需要将之前下载的视频都批量在编号与标题之间加上一个分隔符。

这种事情当然不能一个一个改。打开控制台，切换到指定目录，进入python3控制台输入几行代码即可解决问题：

```python
>>> for oldname in os.listdir():
...     if '.mp4' in oldname:
...        newname = oldname[0:3] + '_' + oldname[3:]
...        os.rename(oldname, newname)
...
>>>
```

再看文件，已经变成了：

```
001_视频001名字随便.mp4
002_视频002名字随便.mp4
...
...
```


