---
title: Python对象创建的过程
date: 2017-06-07
tags: Python
categories: code
---

```
# coding: utf-8
"""
对象初始化过程及元类
元类主要使用type函数生成元类 参考： http://tips.pyhub.cc/zh/latest/2016-05-02-Class-and-Metaclass-ii/
type(object) -> the object's type
type(name, bases, dict) -> a new type
class 定义类的语法实际上转化为 type(name, bases, dict)，其中 name 参数为类的名字，bases 为继承父类的元组，dict 为类的属性和方法：
"""


class A(object):
    """__new__ 不返回对象"""
    print '* A01'

    def __init__(self):
        print "* A02", self

    def __new__(cls, *args, **kwargs):
        print "* A03", cls
        return ''


class B(object):
    """__new__ 返回object对象"""
    print '* B01'

    def __init__(self):
        print "* B02", self

    def __new__(cls, *args, **kwargs):
        print "* B03", cls
        cv = object.__new__(cls, *args)
        return cv


class MetaC(type):
    """元类 __new__ 返回要创建的类(C)的对象c"""
    print '* MetaC01'

    def __new__(mcs, cls, base, attrs):
        print "* MetaC03", mcs, cls
        return super(MetaC, mcs).__new__(mcs, cls, base, attrs)


class C(object):
    __metaclass__ = MetaC

    def __init__(self):
        print "* C02", self


if __name__ == "__main__":
    print '--------- A ---------'
    a = A()
    print '--------- B ---------'
    b = B()
    print '--------- C ---------'
    c = C()
```
