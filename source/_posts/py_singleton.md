---
title: Python单列模式的几个实现方式
date: 2017-06-07 10:39:23
tags:
- Python
- Singleton
- 单列模式
categories: code
---

 单列模式的实现方式，设置元类和那个内部方法的知识和装饰器

```
# coding=utf-8
"""
单列模式的几种实现方法
"""

class Singleton(object):
    _instance = None

    def __new__(cls, *args, **kwargs):
        # __new__ 方法在创建对象过程中检查类属性_instance是否为空，不为空就直接返回
        if cls._instance:
            return cls._instance
        cls._instance = cv = object.__new__(cls, *args, **kwargs)
        return cv

    def __init__(self, v):
        """单列方法不应该有此方法"""
        self.v = v


class Singleton1(object):
    """
    这不是一个严格单列类 因为：
      s1 = Singleton(1)
      s2 = Singleton(2)
      print s1 is s2  ＃ False
    """
    _state = {}

    def __new__(cls, *args, **kargs):
        instance = object.__new__(cls, *args, **kargs)
        instance.__dict__ = cls._state
        return instance

    def __init__(self, v):
        self.v = v


class SingletonMetaClass(type):
    """单列模式元类"""
    def __init__(cls, name, bases, dict):
        super(SingletonMetaClass, cls).__init__(name, bases, dict)
        cls._instance = None

    def __call__(cls, *args, **kargs):
        if cls._instance is None:
            cls._instance = super(SingletonMetaClass, cls).__call__(*args, **kargs)
        return cls._instance


class SingletonWithMeta(object):
    """基于元类的单列类"""
    __metaclass__ = SingletonMetaClass

    def __init__(self, v):
        self.v = v


def singleton_decorator(cls):
    """单列描述类"""
    instances = {}

    def _singleton(*args, **kw):
        # print args
        if cls not in instances:
            instances[cls] = cls(*args, **kw)

        return instances[cls]
    return _singleton


@singleton_decorator
class SingletonWithDecorator(object):
    a = 1

    def __init__(self, v=0):
        self.v = v


if __name__ == "__main__":
    s1 = Singleton(1)
    s2 = Singleton(2)
    print s1 is s2
    print s1.v, s2.v

    s3 = Singleton1(3)
    s4 = Singleton1(4)
    print s3 is s4
    print s3.v, s4.v

    s5 = SingletonWithMeta(5)
    s6 = SingletonWithMeta(6)
    print s5 is s6
    print s5.v, s6.v

    s7 = SingletonWithDecorator(7)
    s8 = SingletonWithDecorator(8)
    print s7 is s8
    print s7.v, s8.v
```
执行

```
True
2 2
False
4 4
True
5 5
True
7 7
```
