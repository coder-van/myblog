---
title: python 多继承顺序
date: 2017-06-07
tags:
- python
- 基础
- 多继承
categories: code
---

Python 不同于Java，Python实现了多继承，Python类对象有mro()方法打印继承顺序，但是这个打印的执行顺序和实例初始化顺序是不同的


```
# coding=utf-8

"""
多继承的调用顺序
类的 mro()方法可以打印类的继承顺序
"""


class Init(object):
    def __init__(self, value):
        print 'Init init'
        self.val = value
        print 'value:', self.val


class Add2(Init):
    def __init__(self, val):
        super(Add2, self).__init__(val)
        print 'Add2 init'
        self.val += 2
        print 'value:', self.val


class Mul5(Init):
    def __init__(self, val):
        super(Mul5, self).__init__(val)
        print 'Mul5 init'
        self.val *= 5
        print 'value:', self.val


class Pro(Add2, Mul5):
    # 在解释时执行
    print 'Pro class init'

    def __init__(self, val):
        super(Pro, self).__init__(val)
        print 'Pro init'

    print 'Pro class init again'


class Inc(Pro):
    p = super(Pro)

    def __init__(self, val):
        # self.p.__init__(val)
        super(Inc, self).__init__(val)
        print 'Inc init'
        self.val += 1


print Inc.mro(), "\n Inc val:", Inc(5).val
```

运行结果: 注意Mul5先于Add2初始化
```
Pro class init
Pro class init again
[<class '__main__.Inc'>, <class '__main__.Pro'>, <class '__main__.Add2'>, <class '__main__.Mul5'>, <class '__main__.Init'>, <type 'object'>]
Init init
value: 5
Mul5 init
value: 25
Add2 init
value: 27
Pro init
Inc init
Inc val: 28

```
