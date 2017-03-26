---
layout: post
title:  "nginx日志配置和自动分割脚本"
date:   2017-03-25 22:30:05
categories: 
tags:  nginx
excerpt: nginx日志的配置和自动分割日志脚本。
---

### 日志自定义



```
	log_format main '$remote_addr ($http_x_forwarded_for) - $remote_user [$time_local] "$request" '
					'$status $body_bytes_sent "$http_referer" '
					'"$http_user_agent" '
					'Proxy to: $upstream_addr [$upstream_response_time] [$request_time]';
	access_log  /log/nginx/access.log   main;	
	error_log  /log/nginx/error.log  error;
```

自定义日志输出格式和位置。

作为反向代理时，要在头部使用 X-Forwarded-For 传真实IP。

### 日志自动分割

cron_cut_nginx_log.sh

```
#!/bin/bash
# auto cut nginx log 
# crontab -e 
# 0 0 * * * /home/yaoyan/bin/cron_cut_nginx_log.sh > /dev/null 2>&1
#

NGINXPID="/home/yaoyan/app/nginx/nginx.pid"
LOG_PATH="/log/nginx"
DATE_FLAG=$(date -d "yesterday" +%Y_%m_%d)

mv ${LOG_PATH}/error.log  ${LOG_PATH}/${DATE_FLAG}_error.log
mv ${LOG_PATH}/access.log  ${LOG_PATH}/${DATE_FLAG}_access.log

kill -USR1 $(cat ${NGINXPID})
```

  * ```kill -USR1```   向nginx进程发送重新打开日志文件的信号。