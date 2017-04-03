---
layout: post
title:  "Tomcat日志位置设置"
date:   2017-04-03 10:30:05
categories: 
tags:  tomcat
excerpt: 修改tomcat默认输出的日志、控制台记录文件的位置。
---

Tomcat在使用时，相关logs的设置是在conf目录下的logging.properties配置文件修改，默认为：```${catalina.base}/logs```， 我们可以修改为自定义的路径，比如习惯挂日志存储，可以修改为```/log/tomcat```之类。但修改了配置文件后，发现在tomcat下的logs里会有控制台日志```catalina.out```和 ```localhost_access_log.<日期>.txt```输出。需要进一步修改设置，使控制台catalina.out文件和access日志也输出到我们指定的地方。

### 1. 修改catalina.out文件输出位置


通过对bin路径下相关脚本的搜索，可以看到在```catalina.sh```中195-197行（tomcat-7.0.76）：

```
if [ -z "$CATALINA_OUT" ] ; then
  CATALINA_OUT="$CATALINA_BASE"/logs/catalina.out
fi
```

那么我们只要在catalina.sh文件里，加上 **CATALINA_OUT** 的值即可。

如：

```
CATALINA_OUT=/log/tomcat/catalina.out
```

注意： 需要提前创建/log/tomcat文件夹，就算是logging.properties配置的其他日志也是在/log/tomcat也不行，logging.properties里设置的文件夹不存在会自己创建，但cCATALINA_OUT的文件夹不存在不会自己创建，且因CATALINA_OUT被使用在前，所以会启动会报错并终止。

```
[tomcat@test-140 apache-tomcat-7.0.76]$ bin/startup.sh 
Using CATALINA_BASE:   /usr/users/tomcat/app/apache-tomcat-7.0.76
Using CATALINA_HOME:   /usr/users/tomcat/app/apache-tomcat-7.0.76
Using CATALINA_TMPDIR: /usr/users/tomcat/app/apache-tomcat-7.0.76/temp
Using JRE_HOME:        /
Using CLASSPATH:       /usr/users/tomcat/app/apache-tomcat-7.0.76/bin/bootstrap.jar:/usr/users/tomcat/app/apache-tomcat-7.0.76/bin/tomcat-juli.jar
touch: 无法创建"/log/tomcat/catalina.out": 没有那个文件或目录
/usr/users/tomcat/app/apache-tomcat-7.0.76/bin/catalina.sh:行417: /log/tomcat/catalina.out: 没有那个文件或目录
```

所以需要先创建/log/tomcat目录：

```
mkdir /log/tomcat
```

再次启动后，查看/log/tomcat中的文件：

```
[tomcat@test-140 apache-tomcat-7.0.76]$ ls -ls /log/tomcat/
总用量 20
8 -rw-r--r--. 1 tomcat usr 6160 4月   3 10:43 catalina.2017-04-03.log
8 -rw-r--r--. 1 tomcat usr 6160 4月   3 10:43 catalina.out
0 -rw-r--r--. 1 tomcat usr    0 4月   3 10:43 host-manager.2017-04-03.log
4 -rw-r--r--. 1 tomcat usr  477 4月   3 10:43 localhost.2017-04-03.log
0 -rw-r--r--. 1 tomcat usr    0 4月   3 10:43 manager.2017-04-03.log
[tomcat@test-140 apache-tomcat-7.0.76]$ 
```

可以看到 ```catalina.out``` 已输出到/log/tomcat中。


### 2. access日志位置修改

access日志在```server.xml```中配置，配置比较直观：

```
        <!-- Access log processes all example.
             Documentation at: /docs/config/valve.html
             Note: The pattern used is equivalent to using pattern="common" -->
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log." suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
```

 在```server.xml```文件的137行（tomcat-7.0.76）处修改 **directory** 的值即可。

如修改为：

```
       <Valve className="org.apache.catalina.valves.AccessLogValve" directory="/log/tomcat"
```

再次重启tomcat后，查看/log/tomcat中的文件：

```
[tomcat@test-140 apache-tomcat-7.0.76]$ ll /log/tomcat/
总用量 36
-rw-r--r--. 1 tomcat usr 12320 4月   3 10:57 catalina.2017-04-03.log
-rw-r--r--. 1 tomcat usr 12320 4月   3 10:57 catalina.out
-rw-r--r--. 1 tomcat usr     0 4月   3 10:43 host-manager.2017-04-03.log
-rw-r--r--. 1 tomcat usr   953 4月   3 10:57 localhost.2017-04-03.log
-rw-r--r--. 1 tomcat usr     0 4月   3 10:57 localhost_access_log.2017-04-03.txt
-rw-r--r--. 1 tomcat usr     0 4月   3 10:43 manager.2017-04-03.log
[tomcat@test-140 apache-tomcat-7.0.76]$ 
```

```localhost_access_log```文件已输出在其下。
