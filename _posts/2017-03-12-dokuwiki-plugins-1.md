---
layout: post
title:  "Dokuwiki的一些插件使用记录（一）"
date:   2017-03-13 22:06:05
categories: Web
tags: Dokuwiki Plugin
excerpt: Dokuwiki的一些插件使用记录（一）。
---

最近在研究Dokuwiki系统，准备试试看能不能解决问题。发现Dokuwiki确实是一个很强大的东西，wiki这种自由协作的思路在一个团队中确实能有许多作用。

单纯的wiki并不能满足我们的需求，还好Dokuwiki有丰富的插件，并且简单易用。Dokuwiki基础用法什么的不用过多赘述，下面总结一下几个插件的使用。


### blog plugin

这个可以将某些命名空间改造成论坛或者博客形式，接受用户浏览和快速的发表新页面。

修改默认文章模版： **lib/plugins/blog/_template.txt** ，这种模仿博客模式，便于引导用户按照一定的格式发表页面。还可以加上discussion插件，这不就冒充bbs了么。

![](/images/posts/Snip20170313_3.png)

该blog插件显示文章列表，是依赖**include**插件

所以修改列表风格和鼠标响应事件：

  * **lib/plugins/include/style.css**
  * **lib/plugins/include/script.js**

这个include我只在这个地方用，所以可以放心改。

### nslist Plugin

>官方先抄下来，目前还没专门用到它。

Syntax and Usage

You can see the syntax below. Just replace namespace with the namespace you want to list.

<code> {{nslist>namespace}} </code>

parameters

The nslist plugin supports three parameters:

nodate

To remove the date add the parameter nodate like:

<code> {{nslist>namespace nodate}} </code>

nodsort

In order to sort the pages alphabetically, add the parameter nodsort:

<code> {{nslist>namespace nodsort}} </code>

depth

To also show pages from subnamespaces add the desired depth (default is 1):

<code> {{nslist>namespace 7}} </code>




### userpagecreate Plugin

用户个人主页自动生成插件。当用户登录时，自动在指定的命名空间下，为用户生成一个用户页面。可以设置模版，由默认的模版生成该页面。

设置管理里该插件有三个选项:

  * target: The user page ID prefix
  * template: The user page template (page or namespace)
  * create_summary: The summary for the page creation(s)

Intial setup

Got to the plugin configuration dialog: admin-menu ⇒ configuration ⇒ plugin userpagecreate
Configure the namespace where the userpages should be generated, set “plugin userpagecreate target” to e.g. “user:”
Then define which template is to be used for the new userpage by setting the “plugin userpagecreate template” configuration option to e.g. “template:usertemplate:”
create the e.g. “template:usertemplate:start”. For this purpose you can use all template replacement patterns.
Further patterns can be used if your used auth-backend provides them (e.g. LDAP)
:!: Please note, that the userpage will be created after the first login (not when the user account is set up!)

默认模版，需要在模版的 **tpl_header.php** 的  __tpl_userinfo(); /* 'Logged in as ...' */__ 之后添加：

```
//added
$pageid = "users:home_" . $INFO['client'];
$wlink = wl( $pageid );
echo "<a href='$wlink' class='action'>Userpage </a>";
//end mod
```

### changes

查看变更的页面，我主要用在用户主页上，显示该用户最近更改过的页面。

<code>{{changes>user = @USER@}}</code>

### starred

用户收藏页面插件。依赖 sqlite plugin。安装后需要在. 再模版的main.php合适位置添加:

```php
<?php
      //Starred
      $starred = plugin_load('action','starred');
      if ($starred) $starred->tpl_starred();
?>
```
在页面位置就会出现一个星星，默认是灰色的，可以鼠标点亮，即收藏该页面。

查看用户收藏：

  *  <code>{ {starred} } </code>：现实当前用户的收藏
  *  <code>{\{starred>min}\}</code> ：最小化显示模式，没有收藏时候不会提示“You currently have no starred pages.“这句英文： 
  *  <code>{{starred>min|5}}</code> 或 <code>{{starred|5}}</code>： 加上条数限制.

这个插件我也是用在用户主页上，显示该用户的收藏。

>发现这个markdown里输入两个{  }连用的wiki语法，放在代码里也不行。。。。无语

### 修改字体大小
模版css/basic.less  第22行位置。

```
body {
    font: normal 100%/1.4 "宋体", Arial, sans-serif;
    /* default font size: 100% => 16px; 93.75% => 15px; 87.5% => 14px; 81.25% => 13px; 75% => 12px */
    -webkit-text-size-adjust: 100%;
}
```

### 修改默认时区

直接在init.php里修改吧

```
date_default_timezone_set('PRC');
```

暂时先记这么多吧。





