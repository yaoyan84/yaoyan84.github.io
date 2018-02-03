---
layout: post
title:  "python urllib3 模块"
date:   2018-01-27 23:00:05
categories: python
tags:  python urllib3
excerpt: "python urllib3 模块。"
---

## 发起请求
首先，要加载rullib3模块：
```python
>>> import  urllib3
```
需要一个 [`PoolManager`](http://urllib3.readthedocs.io/en/latest/reference/index.html#urllib3.poolmanager.PoolManager "urllib3.poolmanager.PoolManager") 实例来发起请求。这个对象处理所有的连接缓冲和线程安全性的细节，所以我们不必处理那些细节：
```python
>>> http  =  urllib3.PoolManager()
```
然后使用 **request()** 方法来发起请求。

```python
>>> r = http.request('GET', 'http://httpbin.org/robots.txt')
>>> r.data
b'User-agent: *\nDisallow: /deny\n'
```
**request()** 方法返回一个[`HTTPResponse`](http://urllib3.readthedocs.io/en/latest/reference/index.html#urllib3.response.HTTPResponse "urllib3.response.HTTPResponse") 对象。

可以使用 **request()** 发起请求时使用任何HTTP变量：
```python
>>> r = http.request(
...     'POST',
...     'http://httpbin.org/post',
...     fields={'hello': 'world'})
```
## 响应内容

[`HTTPResponse`](http://urllib3.readthedocs.io/en/latest/reference/index.html#urllib3.response.HTTPResponse "urllib3.response.HTTPResponse") 对象提供了 **status**、**data**、**header** 属性：
```
>>> r = http.request('GET', 'http://httpbin.org/ip')
>>> r.status
200
>>> r.data
b'{\n  "origin": "104.232.115.37"\n}\n'
>>> r.headers
HTTPHeaderDict({'Content-Length': '33', ...})
```

### JSON内容
可以从data属性中 编码和反序列化出JSON内容：
```python
>>> import  json  
>>> r  =  http.request('GET',  'http://httpbin.org/ip')  
>>> json.loads(r.data.decode('utf-8'))  
{'origin': '127.0.0.1'}
```
### 二进制内容

响应的data属性始终以字节字符串表是响应的内容：
```python
>>> r  =  http.request('GET',  'http://httpbin.org/bytes/8')  
>>> r.data  
b'\xaa\xa5H?\x95\xe9\x9b\x11'
```
#### 注意：

对一个稍大的响应，有时将响应数据流式化（[stream](http://urllib3.readthedocs.io/en/latest/advanced-usage.html#stream)）更好。

## 请求数据

### 请求头（Headers）
可以在**request()** 方法的 ```headers``` 参数中使用一个字典指定请求头：
```python
>>> r = http.request(
...     'GET',
...     'http://httpbin.org/headers',
...     headers={
...         'X-Something': 'value'
...     })
>>> json.loads(r.data.decode('utf-8'))['headers']
{'X-Something': 'value', ...}
```
### 查询参数
对于```GET```、```HEAD```和```DELETE```请求，可以通过在```fields```参数中使用一个字典来简单的传递参数：
```python
>>> r = http.request(
...     'GET',
...     'http://httpbin.org/get',
...     fields={'arg': 'value'})
>>> json.loads(r.data.decode('utf-8'))['args']
{'arg': 'value'}
```
对于```POST```、```PUT```请求，需要手动在URL中编码查询参数：
```python
>>> from urllib.parse import urlencode
>>> encoded_args = urlencode({'arg': 'value'})
>>> url = 'http://httpbin.org/post?' + encoded_args
>>> r = http.request('POST', url)
>>> json.loads(r.data.decode('utf-8'))['args']
{'arg': 'value'}
```
### 表格数据
对于```POST```、```PUT```请求，urllib3将把在为**request()** 提供的```fields```参数中的字典自动的表格化编码。
```python
>>> r = http.request(
...     'POST',
...     'http://httpbin.org/post',
...     fields={'field': 'value'})
>>> json.loads(r.data.decode('utf-8'))['form']
{'field': 'value'}
```
### JSON
在调用**request()** 方法时，在指定编码后的数据作为```body```参数，并设置```Content-Type```请求头，可以发送一个JSON格式的请求。

```python
>>> import json
>>> data = {'attribute': 'value'}
>>> encoded_data = json.dumps(data).encode('utf-8')
>>> r = http.request(
...     'POST',
...     'http://httpbin.org/post',
...     body=encoded_data,
...     headers={'Content-Type': 'application/json'})
>>> json.loads(r.data.decode('utf-8'))['json']
{'attribute': 'value'}
```
### 文件和二进制数据
要使用```multipart/form-data```编码上传文件，可以使用与表格数据相同的方法，使用一个(文件名， 文件数据)的元组来定义文件字段：
```python
>>> with open('example.txt') as fp:
...     file_data = fp.read()
>>> r = http.request(
...     'POST',
...     'http://httpbin.org/post',
...     fields={
...         'filefield': ('example.txt', file_data),
...     })
>>> json.loads(r.data.decode('utf-8'))['files']
{'filefield': '...'}
```
指定文件名不是强制要求的，它只是为了符合浏览器的行为习惯。可以在元组传递第三个元素来明确指定文件的MIME类型：
```python
>>> r = http.request(
...     'POST',
...     'http://httpbin.org/post',
...     fields={
...         'filefield': ('example.txt', file_data, 'text/plain'),
...     })
```
简单的指定```body```参数发送原始二进制数据，也推荐设置```Content-Type```请求头：
```python
>>> with open('example.jpg', 'rb') as fp:
...     binary_data = fp.read()
>>> r = http.request(
...     'POST',
...     'http://httpbin.org/post',
...     body=binary_data,
...     headers={'Content-Type': 'image/jpeg'})
>>> json.loads(r.data.decode('utf-8'))['data']
b'...'
```
## 证书验证
使用SSL证书认证一直被强烈推荐。默认的，urllib3不验证HTTPS请求。

为了能够验证需要设置根证书。最简单可靠的方法时使用[certifi](https://certifi.io/en/latest)包，它提供Mozilla的根证书捆绑。

```
pip  install  certifi
```
也可以使用urllib3的secure扩展来安装certifi：
```
pip  install  urllib3[secure]
```
警告：

如果使用python2，将需要额外的包。之后会详述。

一旦有了证书之后，可以创建一个 **PoolManager** 在请求的时候验证证书：
```python
>> import certifi
>>> import urllib3
>>> http = urllib3.PoolManager(
...     cert_reqs='CERT_REQUIRED',
...     ca_certs=certifi.where())
```
 **PoolManager** 将自动操作证书验证，并在验证失败时给出[`SSLError`](http://urllib3.readthedocs.io/en/latest/reference/index.html#urllib3.exceptions.SSLError "urllib3.exceptions.SSLError")错误提示：

```python
>>> http.request('GET', 'https://google.com')
(No exception)
>>> http.request('GET', 'https://expired.badssl.com')
urllib3.exceptions.SSLError ...
```
## 在python2中验证证书
老版本的Python2使用一个缺乏[SNI support](http://urllib3.readthedocs.io/en/latest/advanced-usage.html#sni-warning)支持并且安全更新延迟落后的SSL构建的。因此它推荐使用[pyOpenSSL](https://pyopenssl.readthedocs.io/en/latest/)。

如果使用urllib3的secure扩展来安装，所有在python2中验证证书需要的包都将被安装：
```
pip  install  urllib3[secure]
```
如果想要手动安装包，需要安装`pyOpenSSL`, `cryptography`, `idna`, and `certifi`.

一旦安装，就可以告诉urllib3使用pyOpenSSL：
```python
>>> import  urllib3.contrib.pyopenssl  
>>> urllib3.contrib.pyopenssl.inject_into_urllib3()
```
最终，创建一个 **PoolManager** 在执行时验证证书：
```python
>>> import certifi
>>> import urllib3
>>> http = urllib3.PoolManager(
...     cert_reqs='CERT_REQUIRED',
...     ca_certs=certifi.where())
```
## 使用超时
超时允许你控制请求在被放弃之前允许运行多久。简单的说，你能对**request()** 方法指定一个浮点数作为超时参数：
```python
>>> http.request(
...     'GET', 'http://httpbin.org/delay/3', timeout=4.0)
<urllib3.response.HTTPResponse>
>>> http.request(
...     'GET', 'http://httpbin.org/delay/3', timeout=2.5)
MaxRetryError caused by ReadTimeoutError
```
可以使用**Timeout** 实例来更细颗粒度的分别指定连接超时和读超时：

```python
>>> http.request(
...     'GET',
...     'http://httpbin.org/delay/3',
...     timeout=urllib3.Timeout(connect=1.0))
<urllib3.response.HTTPResponse>
>>> http.request(
...     'GET',
...     'http://httpbin.org/delay/3',
...     timeout=urllib3.Timeout(connect=1.0, read=2.0))
MaxRetryError caused by ReadTimeoutError
```
如果想让所有的请求都有一样的超时，可以在**PoolManager**级别指定超时：
```python
>>> http  =  urllib3.PoolManager(timeout=3.0)  
>>> http  =  urllib3.PoolManager(  
... timeout=urllib3.Timeout(connect=1.0,  read=2.0))
```
在**request()** 指定`timeout`将覆盖池级别的超时。
## 重试请求
urllib3 能自动重试幂等请求。同样的机制也处理重定向。能使用**request()** 方法中的`retries`参数控制重试。默认的，urllib3将重试请求3次，跟进3次重定向。


修改重试的数只需给一个整数：

```python
>>> http.requests('GET',  'http://httpbin.org/ip',  retries=10)
```

禁用所有重试和重定向，指定一个逻辑值`retries=False`：

```python
>>> http.request(
...     'GET', 'http://nxdomain.example.com', retries=False)
NewConnectionError
>>> r = http.request(
...     'GET', 'http://httpbin.org/redirect/1', retries=False)
>>> r.status
302
```

禁用重定向但保留重试，指定逻辑值`redirect=False`：

```python
>>> r  =  http.request(  
... 'GET',  'http://httpbin.org/redirect/1',  redirect=False)  
>>> r.status  
302
```
使用**Retry**实例可以更细颗粒度的控制。这个类允许你更大程度地控制请求如何重试。

例如，做3次重试，但仅限于2次重定向：
```python
>>> http.request(
...     'GET',
...     'http://httpbin.org/redirect/3',
...     retries=urllib3.Retry(3, redirect=2))
MaxRetryError
```
你也可以禁用过多重定向的异常，只返回302响应:
```python
>>> r  =  http.request(  
... 'GET',  
... 'http://httpbin.org/redirect/3',  
... retries=urllib3.Retry(  
... redirect=2,  raise_on_redirect=False))  
>>> r.status  
302
```
如果想要使所有的连接都服从同样的重试策略，可以在**PoolManager**级指定重试：
```python
>>> http = urllib3.PoolManager(retries=False)
>>> http = urllib3.PoolManager(
...     retries=urllib3.Retry(5, redirect=2))
```
你还可以在`request()`方法指定`retries`覆盖池级别的重试策略。

## 错误和异常

urllib3 包装低级异常, 例如:

```python
>>> try:
...     http.request('GET', 'nx.example.com', retries=False)
>>> except urllib3.exceptions.NewConnectionError:
...     print('Connection failed.')
```
参见 [`exceptions`](http://urllib3.readthedocs.io/en/latest/reference/index.html#module-urllib3.exceptions "urllib3.exceptions") 查看全部异常的列表。

## 日志

如果你使用标准库 [`logging`](https://docs.python.org/3.5/library/logging.html#module-logging "(in Python v3.5)") 模块，urllib3将会发出几个日志.在某些情况下，这是不可取的. 你可以使用标准的logger接口来更改urllib3日志记录器的日志级别:
```python
>>> logging.getLogger("urllib3").setLevel(logging.WARNING)
```
>[`原文地址`](https://urllib3.readthedocs.io/en/latest/user-guide.html)
