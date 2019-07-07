---
layout: post
title:  "python日志模块logging"
date:   2019-06-06 17:00:05
categories: python3
tags:  python3 logging
excerpt: "使用logging模块输出日志"
---

python可以使用**logging** 模块输出日志，能满足大部分场景下的日志输出需求。

[logging 官方文档](https://docs.python.org/zh-cn/3/library/logging.html)

## 主要对象

logging模块下主要的几个常用对象有 **Logger** 、**Handler** 、 **Formatter**

* Logger 日志输出器是日志输出的主要接口，日志名称、输出级别、输出日志操作都是调用该对象的方法完成。
* Handler 对象可理解为日志输出的目标定义，一个Logger对象可以有多个Handler，可以同时将日志输出到多个目标。
* Formatter 对象定义日志输出的格式。

## Logger 日志记录器

在使用logging模块时，都必须至少定义一个Logger对象。

```python
log = logging.getLogger(name)
```

参数name为日志记录器名称，可选，如未给name，则日志名称为root。

可以使用 ```log.getChild(childName)``` 获得一个子记录器。

例如：

```python
log = logging.getLogger("AppName")
log2 = log.getChild("Section1")
```

log记录器的名称为 “AppName”，而log2的名称为 “AppName.Section1”。

* 如果父名称未定义，即默认root，则子名称不会含有 root. *

### logger 的主要方法

```Logger.setLevel(level)``` 定义日志级别。 在日志输出器定义日志级别。如果该级别大于Handler的日志级别，则以该级别为准。

```Logger.getChild(suffix)``` 获得一个子对象。
```Logger.addHandler(hdlr)``` 为日志输出器增加一个Handler（输出目标）。
```Logger.debug(msg, *args, **kwargs)``` 输出debug级别日志。

```python
Logger.info(msg, *args, **kwargs)
Logger.warning(msg, *args, **kwargs)
Logger.error(msg, *args, **kwargs)
Logger.critical(msg, *args, **kwargs)
```

以上为输出各级别的日志。

## Handler 对象

Handler对象是定义日志输出的目标，一般使用常用的Handler有

* logging.StreamHandler() 流输出，默认使用 sys.stderr 使用控制台日志输出
* logging.FileHandler(filename, mode='a', encoding=None, delay=False) 文件日志输出

### Handler对象常用方法

```Handler.setLevel(level)``` 设置输出级别，如果大于Logger的级别，则按此输出。
```Handler.setFormatter(fmt)``` 设置输出日志的格式。

## Formatter 对象

Formatter 对象是日志输出格式的定义。

日志格式可以使用日志记录的一些属性来定义，如：

```python
formatter = logging.Formatter(
    '%(asctime)s - %(name)s - %(levelname)s: - %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S')
```

日志输出的样例为：

```log
2019-06-10 11:22:40 - root - INFO: - test info!!!
```

## 日志级别

日志级别和对应的值如下：

| 级别 | 数值 |
| --- | --- |
| CRITICAL | 50 |
| ERROR | 40 |
| WARNING | 30 |
| INFO | 20 |
| DEBUG | 10 |
| NOTSET | 0 |

## 日志记录的属性

| Attribute name | Format | Description |
| --- | --- | --- |
| args | You shouldn’t need to format this yourself. | The tuple of arguments merged into msg to produce message, or a dict whose values are used for the merge (when there is only one argument, and it is a dictionary). |
| asctime | %(asctime)s | Human-readable time when the LogRecord was created. By default this is of the form ‘2003-07-08 16:49:45,896’ (the numbers after the comma are millisecond portion of the time). |
| created | %(created)f | Time when the LogRecord was created (as returned by time.time()). |
| exc_info | You shouldn’t need to format this yourself. | Exception tuple (à la sys.exc_info) or, if no exception has occurred, None. |
| filename | %(filename)s | Filename portion of pathname. |
| funcName | %(funcName)s | Name of function containing the logging call. |
| levelname | %(levelname)s | Text logging level for the message ('DEBUG', 'INFO', 'WARNING', 'ERROR', 'CRITICAL'). |
| levelno | %(levelno)s | Numeric logging level for the message (DEBUG, INFO, WARNING, ERROR, CRITICAL). |
| lineno | %(lineno)d | Source line number where the logging call was issued (if available). |
| module | %(module)s | Module (name portion of filename). |
| msecs | %(msecs)d | Millisecond portion of the time when the LogRecord was created. |
| message | %(message)s | The logged message, computed as msg % args. This is set when Formatter.format() is invoked. |
| msg | You shouldn’t need to format this yourself. | The format string passed in the original logging call. Merged with args to produce message, or an arbitrary object (see Using arbitrary objects as messages). |
| name | %(name)s | Name of the logger used to log the call. |
| pathname | %(pathname)s | Full pathname of the source file where the logging call was issued (if available). |
| process | %(process)d | Process ID (if available). |
| processName | %(processName)s | Process name (if available). |
| relativeCreated | %(relativeCreated)d | Time in milliseconds when the LogRecord was created, relative to the time the logging module was loaded. |
| thread | %(thread)d | Thread ID (if available). |
| threadName | %(threadName)s | Thread name (if available). |

## 完整的demo

将日志分别在文件和控制台进行输出

```python
import logging

log = logging.getLogger("TestA")
log.setLevel(logging.DEBUG)
formatter = logging.Formatter(
    '%(asctime)s - %(name)s - %(levelname)s: - %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S')

hc = logging.StreamHandler()
hc.setLevel(logging.DEBUG)
hc.setFormatter(formatter)

hf = logging.FileHandler(filename="test2.log", encoding='utf-8')
hf.setLevel(logging.DEBUG)
hf.setFormatter(formatter)

log.addHandler(hc)
log.addHandler(hf)

log.info("test info!!!")
log.debug("test debug!!!")
log.warning("test warning!!!")
log.error("test  error!!!")

log2 = log.getChild('secton1')
log2.info("xxxxxx")
```
