---
layout: post
title:  "python虚拟环境使用virtualenv（windows示例）"
date:   2017-07-23 23:00:05
categories: Python
tags:  Python  virtualenv
excerpt: python的虚拟环境模块virtualenv使用。
---

python在实际使用中，每个项目对各种包依赖的版本会稍有不同，需要在同一个服务器上部署依赖不同的项目，可以使用 virtualenv 创建独立的虚拟环境，在虚拟环境种安装特定的第三方包满足单个项目的依赖需求，而又不影响系统本身环境和其他虚拟环境。

### virtualenv 的安装：

```
D:\python>pip install virtualenv
Collecting virtualenv
  Downloading virtualenv-15.1.0-py2.py3-none-any.whl (1.8MB)
    100% |████████████████████████████████| 1.8MB 192kB/s
Installing collected packages: virtualenv
Successfully installed virtualenv-15.1.0
```

### virtualenv 用法提示：

```
D:\python>virtualenv
You must provide a DEST_DIR
Usage: virtualenv [OPTIONS] DEST_DIR

Options:
  --version             show program's version number and exit
  -h, --help            show this help message and exit
  -v, --verbose         Increase verbosity.
  -q, --quiet           Decrease verbosity.
  -p PYTHON_EXE, --python=PYTHON_EXE
                        The Python interpreter to use, e.g.,
                        --python=python2.5 will use the python2.5 interpreter
                        to create the new environment.  The default is the
                        interpreter that virtualenv was installed with
                        (c:\python36\python3.exe)
  --clear               Clear out the non-root install and start from scratch.
  --no-site-packages    DEPRECATED. Retained only for backward compatibility.
                        Not having access to global site-packages is now the
                        default behavior.
  --system-site-packages
                        Give the virtual environment access to the global
                        site-packages.
  --always-copy         Always copy files rather than symlinking.
  --unzip-setuptools    Unzip Setuptools when installing it.
  --relocatable         Make an EXISTING virtualenv environment relocatable.
                        This fixes up scripts and makes all .pth files
                        relative.
  --no-setuptools       Do not install setuptools in the new virtualenv.
  --no-pip              Do not install pip in the new virtualenv.
  --no-wheel            Do not install wheel in the new virtualenv.
  --extra-search-dir=DIR
                        Directory to look for setuptools/pip distributions in.
                        This option can be used multiple times.
  --download            Download preinstalled packages from PyPI.
  --no-download, --never-download
                        Do not download preinstalled packages from PyPI.
  --prompt=PROMPT       Provides an alternative prompt prefix for this
                        environment.
  --setuptools          DEPRECATED. Retained only for backward compatibility.
                        This option has no effect.
  --distribute          DEPRECATED. Retained only for backward compatibility.
                        This option has no effect.
```

### 基本使用方法

Usage:   ```virtualenv [OPTIONS] DEST_DIR```

#### 创建一个虚拟环境

```
D:\python>virtualenv testenv1
Using base prefix 'c:\\python36'
New python executable in D:\python\testenv1\Scripts\python3.exe
Also creating executable in D:\python\testenv1\Scripts\python.exe
Installing setuptools, pip, wheel...done.
```

可以看出，在python目录下，创建了一个名为testenv1的目录，这个目录里就是新建的虚拟环境。进入目录，激活虚拟环境：

```
D:\python>dir
 驱动器 D 中的卷是 新加卷
 卷的序列号是 1897-675D

 D:\python 的目录

2017/07/23  22:36    <DIR>          .
2017/07/23  22:36    <DIR>          ..
2017/07/23  22:36    <DIR>          testenv1
               3 个目录 789,513,146,368 可用字节

D:\python>cd testenv1

D:\python\testenv1>Scripts\activate

(testenv1) D:\python\testenv1>
```

#### 在虚拟环境中安装一个包：

```
(testenv1) D:\python\testenv1>pip install tornado
Collecting tornado
  Downloading tornado-4.5.1-cp36-cp36m-win_amd64.whl (422kB)
    100% |████████████████████████████████| 430kB 237kB/s
Installing collected packages: tornado
Successfully installed tornado-4.5.1

(testenv1) D:\python\testenv1>python
Python 3.6.1 (v3.6.1:69c0db5, Mar 21 2017, 18:41:36) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import tornado
>>> 
```

正常安装tornado，import也正常。
退出虚拟环境，在系统本身的python环境尝试 ```import tornado```

```
(testenv1) D:\python\testenv1>Scripts\deactivate.bat
D:\python\testenv1>
D:\python\testenv1>python
Python 3.6.1 (v3.6.1:69c0db5, Mar 21 2017, 18:41:36) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import tornado
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ModuleNotFoundError: No module named 'tornado'
>>>
```

系统本身的python并没有安装tornado，我们在虚拟环境所做的python环境的变更，只在一个虚拟环境中有效。

#### --system-site-packages  选项，虚拟环境包含系统现有的包

例如： 在操作系统环境安装flask包

```
D:\python>pip install flask
Collecting flask
  Downloading Flask-0.12.2-py2.py3-none-any.whl (83kB)
    100% |████████████████████████████████| 92kB 216kB/s
Collecting Jinja2>=2.4 (from flask)
  Downloading Jinja2-2.9.6-py2.py3-none-any.whl (340kB)
    100% |████████████████████████████████| 348kB 2.1MB/s
Collecting click>=2.0 (from flask)
  Downloading click-6.7-py2.py3-none-any.whl (71kB)
    100% |████████████████████████████████| 71kB 2.9MB/s
Collecting Werkzeug>=0.7 (from flask)
  Downloading Werkzeug-0.12.2-py2.py3-none-any.whl (312kB)
    100% |████████████████████████████████| 317kB 2.9MB/s
Collecting itsdangerous>=0.21 (from flask)
  Downloading itsdangerous-0.24.tar.gz (46kB)
    100% |████████████████████████████████| 51kB 2.0MB/s
Collecting MarkupSafe>=0.23 (from Jinja2>=2.4->flask)
  Downloading MarkupSafe-1.0.tar.gz
Installing collected packages: MarkupSafe, Jinja2, click, Werkzeug, itsdangerous, flask
  Running setup.py install for MarkupSafe ... done
  Running setup.py install for itsdangerous ... done
Successfully installed Jinja2-2.9.6 MarkupSafe-1.0 Werkzeug-0.12.2 click-6.7 flask-0.12.2 itsdangerous-0.24

D:\python>python
Python 3.6.1 (v3.6.1:69c0db5, Mar 21 2017, 18:41:36) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import flask
>>> exit()
```

创建虚拟环境testenv2，使用 ```--system-site-packages``` 参数，该环境可以使用系统已安装的包：

```
D:\python>virtualenv --system-site-packages testenv2
Using base prefix 'c:\\python36'
New python executable in D:\python\testenv2\Scripts\python3.exe
Also creating executable in D:\python\testenv2\Scripts\python.exe
Installing setuptools, pip, wheel...done.

D:\python>cd testenv2

D:\python\testenv2>Scripts\activate

(testenv2) D:\python\testenv2>python
Python 3.6.1 (v3.6.1:69c0db5, Mar 21 2017, 18:41:36) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import flask
>>>
>>>
>>> import tornado
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ModuleNotFoundError: No module named 'tornado'
>>>
```

如未使用该参数，如testenv3，则是一个干净的虚拟环境：

```shell?linenums
D:\python>virtualenv  testenv3
Using base prefix 'c:\\python36'
New python executable in D:\python\testenv3\Scripts\python3.exe
Also creating executable in D:\python\testenv3\Scripts\python.exe
Installing setuptools, pip, wheel...done.

D:\python>cd testenv3

D:\python\testenv3>Scripts\activate

(testenv3) D:\python\testenv3>python
Python 3.6.1 (v3.6.1:69c0db5, Mar 21 2017, 18:41:36) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import flask
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ModuleNotFoundError: No module named 'flask'
>>>
```

### 小结：

 *  virtualenv [OPTIONS] DEST_DIR  创建虚拟环境
 *  执行虚拟环境目录下的 Scripts\activate 激活虚拟环境
 *  执行虚拟环境目录下的 Scripts\deactivate.bat 退出虚拟环境

