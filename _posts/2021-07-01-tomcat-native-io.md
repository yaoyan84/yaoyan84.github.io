---
layout: post
title:  "tomcat启用native io"
date:   2021-07-01 20:15:31
categories: 
tags: tomcat native io
excerpt: 
---

服务器基础编译环境默认安装，并确认依赖（root）：

```bash
yum install apr-devel
```

进入tomcat的bin下，附带有native源码包，找到并解压 **（以下可使用非root操作）**：
```bash
cd /apps/tomcat/bin
tar xzvf tomcat-native.tar.gz
```

进入源码文件中，进行编译设置：
```bash
cd tomcat-native-1.2.28-src/native/
./configure --with-apr=/usr/bin/apr-1-config --with-java-home=$JAVA_HOME --with-ssl=no --prefix=/apps/tomcat
```

编译并安装：
```bash
make && make install
```

修改启动环境，在`LD_LIBRARY_PATH` 中增加设置：
```bash
vi /apps/tomcat/bin/setenv.sh
```
增加：

```bash
LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$CATALINA_HOME/lib
export LD_LIBRARY_PATH
```

修改tomcat配置：
```bash
vi /apps/tomcat/conf/server.xml
```

如果有这一行：
```xml
<Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
```
修改为：
```xml
<Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="off" />
```


修改业务服务的Connector设置中的协议为 `org.apache.coyote.http11.Http11AprProtocol`：
```xml
<Connector port="18810" protocol="org.apache.coyote.http11.Http11AprProtocol"
               connectionTimeout="120000"
               executor="tomcatThreadPool"
               acceptCount="200"
               maxKeepAliveRequests="-1"
               redirectPort="8443"  URIEncoding="UTF-8" server="WWS1.0" />
```

完成。

启动tomcat：

```
......
......
七月 02, 2021 12:12:03 上午 org.apache.catalina.startup.VersionLoggerListener log
信息: 命令行参数：       -Dcatalina.base=/apps/tomcat
七月 02, 2021 12:12:03 上午 org.apache.catalina.startup.VersionLoggerListener log
信息: 命令行参数：       -Dcatalina.home=/apps/tomcat
七月 02, 2021 12:12:03 上午 org.apache.catalina.startup.VersionLoggerListener log
信息: 命令行参数：       -Djava.io.tmpdir=/apps/tomcat/temp
七月 02, 2021 12:12:03 上午 org.apache.catalina.core.AprLifecycleListener lifecycleEvent
信息: 使用APR版本[1.4.8]加载了基于APR的Apache Tomcat本机库[1.2.28]。
七月 02, 2021 12:12:03 上午 org.apache.catalina.core.AprLifecycleListener lifecycleEvent
信息: APR功能：IPv6[true]、sendfile[true]、accept filters[false]、random[true]。
七月 02, 2021 12:12:03 上午 org.apache.coyote.AbstractProtocol init
信息: 初始化协议处理器 ["http-apr-18810"]
七月 02, 2021 12:12:03 上午 org.apache.catalina.startup.Catalina load
信息: Initialization processed in 482 ms
七月 02, 2021 12:12:03 上午 org.apache.catalina.core.StandardService startInternal
信息: 正在启动服务[Catalina]
七月 02, 2021 12:12:03 上午 org.apache.catalina.core.StandardEngine startInternal
信息: Starting Servlet Engine: Apache Tomcat/7.0.109
七月 02, 2021 12:12:03 上午 org.apache.catalina.startup.HostConfig beforeStart
严重: 无法为部署创建目录：[/apps/tomcat/conf/Catalina/localhost]
七月 02, 2021 12:12:03 上午 org.apache.catalina.startup.HostConfig deployDirectory
信息: 把web 应用程序部署到目录 [/apps/tomcat/webapps/ROOT]
七月 02, 2021 12:12:04 上午 org.apache.catalina.startup.HostConfig deployDirectory
信息: Web应用程序目录[/apps/tomcat/webapps/ROOT]的部署已在[176]毫秒内完成
七月 02, 2021 12:12:04 上午 org.apache.coyote.AbstractProtocol start
信息: 开始协议处理句柄["http-apr-18810"]
七月 02, 2021 12:12:04 上午 org.apache.catalina.startup.Catalina start
信息: Server startup in 264 ms
```



