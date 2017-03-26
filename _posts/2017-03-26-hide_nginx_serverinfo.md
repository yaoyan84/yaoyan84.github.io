---
layout: post
title:  "隐藏nginx服务器信息"
date:   2017-03-26 21:30:05
categories: 
tags:  nginx
excerpt: 隐藏nginx响应标头中的服务器信息。
---

客户端能从服务器响应信息中得到服务器的信息和版本号，通过修改配置文件可以使nginx隐藏版本号，但通过配置无法隐藏服务器信息。

响应标头中会有  ```server：nginx``` 的字样。

###  修改nginx源代码：

以 nginx-1.10.1为例：

#### 1. src/http/ngx_http_header_filter_module.c

```
vi src/http/ngx_http_header_filter_module.c
```

找到：

```
static char ngx_http_server_string[] = "Server: nginx" CRLF;
static char ngx_http_server_full_string[] = "Server: " NGINX_VER CRLF;
```

修改为：

```
static char ngx_http_server_string[] = "Server: yaoyan" CRLF;
static char ngx_http_server_full_string[] = "Server: yaoyan"  CRLF;
```
#### 2. src/core/nginx.h

```
vi src/core/nginx.h
```

找到：

```
#define NGINX_VERSION      "1.10.1"
#define NGINX_VER          "nginx/" NGINX_VERSION
```

修改为：

```
#define NGINX_VERSION      "XX"
#define NGINX_VER          "yaoyan/" NGINX_VERSION
```


##### 3. src/http/ngx_http_special_response.c
```
vi src/http/ngx_http_special_response.c
```

找到：

```
static u_char ngx_http_error_tail[] =
"<hr><center>nginx</center>" CRLF
"</body>" CRLF
"</html>" CRLF
```

修改为：

```
static u_char ngx_http_error_tail[] =
"<hr><center>yaoyan</center>" CRLF
"</body>" CRLF
"</html>" CRLF
```

#### 编译安装

configure  make  make install 按照正常思路即可。


完成后，响应的标头中显示的 ```server:yaoyan```

错误页的页脚页显示的是  ```yaoyan/XX```


