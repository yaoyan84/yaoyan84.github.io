---
layout: post
title:  "正式入住Github Pages"
date:   2017-03-11 20:06:05
categories: Web
tags: GitHub Pages Blog
excerpt: 将博客从vps迁移到github。
---

# 正式入住Github Pages

以前一直使用一个国外的VPS，速度一直不怎么快，经常刷新多次都打不开，还偶尔会彻底失联。最近一段时间接触到了Github、markdown，才知道原来还有这么简单便捷的建站方式，相见恨晚。经过一段时间的折腾，决定迁移过来，反正以前的blog也很久不更新了，相当于从头再来。

## 准备工作
1. 注册一个Github账号，有点废话，之前一直有帐号：yaoyan84
2. 创建一个repository，名字使用 **"账户名.github.io"** 的格式
3. 在这个repository的Settings里，找到下方的"GitHub Pages"选项，默认是打开的：

![](/media/files/Snip20170311_3.png)

此时访问地址： https://yaoyan84.github.io 能看到默认readme.md渲染之后的内容。
 
## 准备模版

Github Pages使用Jekyll做生成器，模版资源很丰富，网上有很多大牛做的模版很漂亮，而且都可以fork拿来改改就用，所以要找到模版很简单，挑个自己看着顺眼的就可以。

git clone下自己新建的yaoyan84.github.io仓库：

```
git clone git@github.com:yaoyan84/yaoyan84.github.io.git
```

根据主题的说明修改必要的配置文件，如 **_config.yml** 文件，然后清空 **_posts** 文件夹里别人的文章，将自己的markdown文件到 **_post** 里，push仓库到Github上，即可用浏览器查看效果。



    

 