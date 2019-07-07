---
layout: post
title:  "Python requests 模块批量下载网站图片"
date:   2019-07-07 13:30:05
categories: 
tags:  Python
excerpt: 
---

找了个结构简单的图片站 [social wallpapering](https://www.socwall.com/) 练习下使用 ```requests``` 做爬虫，下载该站点所有的图片。

## 使用到的模块说明

 * [requests 文档](https://2.python-requests.org//zh_CN/latest/index.html)
 * [Beautiful Soup 4 文档](https://www.crummy.com/software/BeautifulSoup/bs4/doc/index.zh.html)

## 源码

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# Created by YaoYan on 2019/7/7

import requests
from bs4 import BeautifulSoup
import logging
import os
import time


def download(file_url, file_name):
    """
    下载文件，保存在当前位置中。
    :param file_url: 需要下载的url
    :param file_name: 保存的文件名
    :return: 无
    """
    download_start_time = time.time()
    r = requests.get(file_url, stream=True)
    file_size = int(r.headers['Content-Length'])
    logger.info(file_name + " : [ " + str(file_size) + " ]")
    chunk_size = 1024
    downloaded_size = 0
    block = "█"
    with open(file_name, 'wb') as fd:
        for chunk in r.iter_content(chunk_size):
            fd.write(chunk)
            downloaded_size = downloaded_size + len(chunk)
            a = round(downloaded_size / file_size * 32)
            b = 32 - a
            print("  --->" + file_name + " |" + block * a + '  ' * b + "| [" +
                  str(downloaded_size) + "/" + str(file_size) + "]" +
                  str(round(downloaded_size / file_size * 100, 2)) + "%", end='\r')
    download_end_time = time.time()
    logger.info("Download file [" + file_name + "] finished in " +
                str(download_end_time - download_start_time) + ' s')


def get_image(url):
    """
    找到URL页面中的download按钮的超链接地址，并下载指向的图片文件。
    :param url: 要下载图片的展示页面的url，其中有download按钮超链接。
    :return:无
    """
    logger.info(' Get image page ' + url)
    r = requests.get(url)
    if r.status_code == 200:
        soup = BeautifulSoup(r.text.encode('utf-8'), features="html.parser")
        image_url = wwwroot + soup.select_one(".download")['href']
        file_name = (image_url.split('/')[-1])
        logger.info("Image file url: " + image_url + " , filename: [ " + file_name + " ]")
        download(image_url, file_name)


def get_page(url):
    """
    从分页页面中找到当前页的图片详情页面的url，并逐个从图片详情页中去进行下载。
    :param url:分页页面地址
    :return:无
    """
    logger.info('Start downlad images from ' + url + ' .......')
    r = requests.get(url)
    if r.status_code == 200:
        soup = BeautifulSoup(r.text.encode('utf-8'), features="html.parser")
        image_pages = soup.select(".image")
        logger.info('find ' + str(len(image_pages)) + ' images in page ' + url)
        for a in image_pages:
            image_page = wwwroot + a['href']
            logger.info('  image url : ' + image_page)
            get_image(image_page)


if __name__ == '__main__':
    # 设置日志参数，记录下载情况
    logger = logging.getLogger()
    formatter = logging.Formatter(
        '[%(asctime)s] [ %(name)s ] [%(filename)s,%(funcName)s,%(lineno)d] [%(levelname)s] %(message)s',
        datefmt='%Y-%m-%d %H:%M:%S')
    logger.setLevel(logging.INFO)
    handle1 = logging.StreamHandler()
    handle1.setLevel(logging.DEBUG)
    handle1.setFormatter(formatter)
    logger.addHandler(handle1)

    # 如无images目录，则创建，并将目录切换到images目录
    if not os.path.exists("images"):
        os.mkdir("images")
    os.chdir("images")

    wwwroot = "https://www.socwall.com"  # 网站目录
    base_url = "https://www.socwall.com/wallpapers/page:"  # 分页基本目录

    logger.info("Get all images from socwall.com start....")
    start_time = time.time()

    # 该网站一共有754个分页，逐页遍历
    for no in range(0, 754):
        logger.info('Start downlad images from page ' + str(no + 1) + '.')
        web_url = base_url + str(no + 1)
        get_page(web_url)
        logger.info('Images from page ' + str(no + 1) + ' has been accomplished.')
    end_time = time.time()
    logger.info("Get all images from socwall.com finished in " + str(end_time - start_time) + ' s')

```

## 效果

执行后，开始逐个从第一页开始下载图片。

![](/images/posts/2019/downloads.gif)

