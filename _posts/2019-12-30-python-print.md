---
layout: post
title:  "Python print输出中字符串与变量"
date:   2019-12-30 13:30:05
categories: 
tags:  Python print
excerpt: 
---

在输出时，需要字符串与变量混合输出。做法有如下：

### 1. 字符串相加

```python
price = 13.2456
unit = "kg"
print("Unit-price: " + str(price) + "/" + unit)
```
输出为：

```
Unit-price: 13.2456/kg
```

这种方法的问题是需要将非字符串类型的变量强制转换为字符串类型，否则会报错 ```TypeError: must be str, not int``` 并且要用很多 ```+``` 号，降低可阅读性。


### 2. 格式化输出方法

使用格式化语法控制变量格式。

```python
price = 13.2456
unit = "kg"
print("Unit-price: %.2f/%s" % (price, unit))
```

输出为：

```
Unit-price: 13.25/kg
```

本方法在输出时可以做格式化控制，同时也可以做到内容跟变量分开，稍微提高点阅读性。但是该方法对变量的顺序有较严格的要求，后面提供的变量元组中的顺序，应与前面格式控制的顺序一致，且变量类型也要符合。

如格式控制中使用的是```%s```，后面提供的变量是```int```或者```float```都可以正常输出。但如果格式控制中使用的是```%d```或者```%f```，后面给的变量却是字符串的话，则会报错误```TypeError: %d format: a number is required, not str```或者```TypeError: must be real number, not str```。

### 3. 使用f-string

```python
price = 13.2456
unit = "kg"
print(f"Unit-price: {price}/{unit}")
```

输出为：

```
Unit-price: 13.2456/kg
```

这种方法在3.6及之后可用，阅读性高，简单方便，不用担心变量顺序和类型问题。
