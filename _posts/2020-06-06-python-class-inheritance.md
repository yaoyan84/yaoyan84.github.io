---
layout: post
title:  "python类的继承"
date:   2020-06-06 19:29:12
categories: 
tags:  python
excerpt: 
---

python 面向对象，类的继承演示

```python
class Animal:
    '''定义类Animal'''
    category = '动物'
    voice = ''

    def show(self):
        print(self.name + '是一只 ' + self.category + ' , 发出声响： ' + self.voice)

    def __init__(self,name1):
        self.name = name1


class Dog(Animal):
    '''定义类Dog，继承至Animal'''
    category = '狗狗'
    voice = '汪汪汪'

    def __init__(self,name1,id1):
        '''重构Animal的构造方法'''
        Animal.__init__(self, name1) # 重构时先继承父类的方法，写法一
        self.id = id1

    def show(self):
        '''重构Animal的show方法'''
        super(Dog,self).show() # 重构时先继承父类的方法，写法二
        print('     -->' + self.name + '的狗牌编号是：' + str(self.id) + '号')


class Cat(Animal):
    '''定义类Cat，继承至Animal'''
    category = '猫咪'
    voice = '喵喵喵'


class Collie(Dog):
    '''定义类Collie，继承至Dog'''
    category = '放羊的狗狗'
    voice = '汪噢~'

    def herding(self):
        '''定义新方法herding'''
        print(self.name + '发出 ' + self.voice +' 声，开始赶羊~')


if __name__=='__main__':
    mimi = Cat('小白')
    wangcai = Dog('旺财',2)

    mimi.show()
    wangcai.show()

    dog2 = Collie('大白',9527)
    dog2.show()
    dog2.herding()
```

执行结果：

```
小白是一只 猫咪 , 发出声响： 喵喵喵
旺财是一只 狗狗 , 发出声响： 汪汪汪
旺财的狗牌编号是：2号
大白的狗牌编号是：9527号
大白是一只 放羊的狗狗 , 发出声响： 汪噢~
大白发出 汪噢~ 声，开始赶羊~
```
