---
layout: post
title:  "NTP服务器时间监控脚本"
date:   2021-03-07 16:17:33
categories: 
tags: NTP-monitor
excerpt: 
---

按照需求对NTP服务器进行监控，如果NTP服务器的时间与本地相差过大，则报警。

获取NTP情况的方法很多，用 **python3** 也实现也非常方便。

有条件安装 ```ntplib``` 可以直接方便的获取到：

```
>>> import ntplib
>>> c = ntplib.NTPClient()
>>> response = c.request('139.199.215.251', version=3)
>>> response.offset
1.48006272315979
```

如果内网环境条件有限，也可以采用```ntpdate```命令获取：

```
root@test:~$ ntpdate -q 139.199.215.251
server 139.199.215.251, stratum 2, offset 1.480354, delay 0.06920
 7 Mar 18:17:22 ntpdate[3463]: step time server 139.199.215.251 offset 1.480354 sec
```

完整脚本如下：

```python
#!/usr/bin/env python
# -*- coding:utf-8 -*-

import os
import configparser
import logging
import time


class Ntp:
    def __init__(self, server_addr):
        self.ntp_server = server_addr

    def get_offset(self):
        r = os.popen("ntpdate -q " + self.ntp_server).read()
        try:
            result = float(r.split()[-2])
        except ValueError:
            result = None
        return result


def post_message(data):
    """调用监控平台演示。"""
    logging.debug(f' 调用监控平台接口，发送告警： {data} ')


def do_check(server, intl, threshold):
    ntp = Ntp(server)
    warn_status = False
    while True:
        offset = ntp.get_offset()
        if offset == None:
            logging.debug(f'获取偏差异常,可能网络波动，{offset}s, 等待下一次轮询')
            continue
        logging.info(f'  {server} -> {offset}s')
        if offset > threshold:
            logging.warning(f'NTP服务器偏差 {offset}s 大于阈值 {threshold}, 请检查')
            if not warn_status:
                warn_status = True
                data = [
                    3, 
                    'NTP监控', 
                    'NTP服务器时间偏差 ' + str(offset) + 's, 请检查！',
                    'NTP时间监控'
                    ]
                post_message(data)
        else:
            logging.info(f'NTP服务器偏差 {offset}s 小于等于阈值 {threshold}。')
            if warn_status:
                warn_status = False
                data = [
                    0, 
                    'NTP监控',   
                    'NTP服务器时间偏差 ' + str(offset) + 's, 恢复正常。',
                    'NTP时间监控'
                    ]
                post_message(data)
        time.sleep(intl)


def main():
    config = configparser.ConfigParser()
    config.read('config.ini')
    ntp_server = config['ntp_server']['server_addr']
    interval = int(config['ntp_server']['interval'])
    threshold = int(config['ntp_server']['threshold'])
    log_format = '%(asctime)s - %(levelname)s - %(message)s'
    logging.basicConfig(filename='nptmonitor.log',format=log_format, level = logging.DEBUG)
    logging.info(f'NPT时间监控启动，服务器 [{ntp_server}], 阈值 [{threshold}]')
    do_check(ntp_server, interval, threshold)


if __name__ == '__main__':
    main()
```

配置文件 ```config.ini``` 范例：

```ini
[ntp_server]
server_addr = 139.199.215.251
interval = 60
threshold = 3600
```

运行结果会记录在日志文件 ```nptmonitor.log```中，超阈值状态发生变化会调用 ```post_message()``` 函数向监控平台发送消息，这个方法内容因为监控接口不同就不展示了。

