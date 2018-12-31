---
layout: post
title:  "python3 使用配置文件configparser"
date:   2018-12-31 10:00:05
categories: python
tags:  python python3 configparser
excerpt: "python3 的configparser模块，读写key-value配置文件"
---

python3 使用配置文件configparser
====

在编程时，要把一些功能、设置做成配置，而不是写死在代码里。所以需要引入配置文件。

python3 提供了 configparser 模块可以方便快速读写类似于微软windows .ini 文件结构的配置文件。

比如：

```ini
[DEFAULT]
ServerAliveInterval = 45
Compression = yes
CompressionLevel = 9
ForwardX11 = yes

[bitbucket.org]
User = hg

[topsecret.server.com]
Port = 50022
ForwardX11 = no
```

模块的使用方法详见[官方文档](https://docs.python.org/3.7/library/configparser.html)


### 测试代码

```python
import configparser

# 创建一个ConfigParser对象
config = configparser.ConfigParser();

# 在默认的 DEFAULT 节点中插入两个选项
config.defaults()['option1'] = 'abc'
config.defaults()['opthon2'] = 'yaoyan.me'
config.defaults()['switch'] = True

# 新增一个 TestOne节点，然后赋值给变量one，这种方法value必须是String
config.add_section('TestOne')
one = config['TestOne']
one['t1'] = '132'
one['t2'] = '133'
one['switch'] = 'False'

# 直接新建一个 Hello节点 并设置key-value对
config['Hello'] = {
    'h1': True,
    'h2': 8766,
    'h3': 'Hello World!'
}

# 打开一个文件test.ini，将配置写入文件
with open('test.ini', 'w', encoding='utf-8') as fp:
    config.write(fp)

# 从文件test.ini中读取配置
config.read('test.ini', 'utf-8')

# 遍历打印配置文件内容
for key in config.keys():
    print("\n### section ： " + key)
    for k in config[key]:
        print('  ' + k + '--->' + config[key][k])

```

代码生成的 ```test.ini``` 内容为：

```ini
[DEFAULT]
option1 = abc
opthon2 = yaoyan.me
switch = True

[TestOne]
t1 = 132
t2 = 133
switch = False

[Hello]
h1 = True
h2 = 8766
h3 = Hello World!
```

代码执行结果：

```

### section ： DEFAULT
  option1--->abc
  opthon2--->yaoyan.me
  switch--->True

### section ： TestOne
  t1--->132
  t2--->133
  switch--->False
  option1--->abc
  opthon2--->yaoyan.me

### section ： Hello
  h1--->True
  h2--->8766
  h3--->Hello World!
  option1--->abc
  opthon2--->yaoyan.me
  switch--->True
```

由结果发现：

  1. 其他section会包含 DEFAULT 中的 key-value。
  2. 如果section中有与 DEFAULT 中冲突的key，以section中的为准。
