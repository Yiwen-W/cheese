---
title: python实现单例模式
date: 2019-09-25 10:24:17
tags: python
categories: 设计模式
---


## 单例模式

- 意图：保证一个类仅有一个实例，并提供一个访问它的全局访问点。

- 主要解决：一个全局使用的类频繁地创建与销毁。

## 应用场景

> 原文：https://www.php.cn/python-tutorials-424115.html

1. Windows的Task Manager（任务管理器）就是很典型的单例模式，想想看，是不是呢，你能打开两个windows task manager吗？

2. windows的Recycle Bin（回收站）也是典型的单例应用。在整个系统运行过程中，回收站一直维护着仅有的一个实例。

3. 网站的计数器，一般也是采用单例模式实现，否则难以同步。

4. 应用程序的日志应用，一般都何用单例模式实现，这一般是由于共享的日志文件一直处于打开状态，因为只能有一个实例去操作，否则内容不好追加。

5. Web应用的配置对象的读取，一般也应用单例模式，这个是由于配置文件是共享的资源。

6. 数据库连接池的设计一般也是采用单例模式，因为数据库连接是一种数据库资源。数据库软件系统中使用数据库连接池，主要是节省打开或者关闭数据库连接所引起的效率损耗，这种效率上的损耗还是非常昂贵的，因为何用单例模式来维护，就可以大大降低这种损耗。

7. 多线程的线程池的设计一般也是采用单例模式，这是由于线程池要方便对池中的线程进行控制。

8. 操作系统的文件系统，也是大的单例模式实现的具体例子，一个操作系统只能有一个文件系统。

9. HttpApplication 也是单位例的典型应用。熟悉ASP.Net(IIS)的整个请求生命周期的人应该知道HttpApplication也是单例模式，所有的HttpModule都共享一个HttpApplication实例.


## 优缺点：

> 原文： https://yq.aliyun.com/articles/70418?spm=a2c4e.11154837.922301.4.6feb20adR90Y1Q

- 优点：
    1. 由于单例模式要求在全局内只有一个实例，因而可以节省比较多的内存空间；
    2. 全局只有一个接入点，可以更好地进行数据同步控制，避免多重占用；
    3. 单例可长驻内存，减少系统开销。
- 缺点：
    1. 单例模式的扩展是比较困难的；
    2. 赋于了单例以太多的职责，某种程度上违反单一职责原则;
    3. 单例模式是并发协作软件模块中需要最先完成的，因而其不利于测试；
    4. 单例模式在某种情况下会导致“资源瓶颈”。


## python的四种实现单例的方法


### 模块

- Python的模块就是天然的单例模式
- 模块在第一次导入时，会生成 `.pyc` 文件
- 第二次导入时，就会直接加载 .pyc 文件，而不会再次执行模块代码

```
# singleton.py
class Singleton(object):
    def test(self):
        pass

singleton = Singleton()

# 使用到的模块中直接引用
from singleton import singleton

singleton.foo()
```

### 使用__new__

```
class Singleton(object):
    _instance = None
    def __new__(cls, *args, **kwargs):  # ①
        if not cls._instance:
            cls._instance = super(Singleton,cls).__new__(cls, *args, **kwargs) # ②
        return cls._instance

class Test(Singleton):
    def __init__(self):
        print("init")
        
test1 = Test()
test2 = Test()
print(id(test1),id(test2))
```
输出：
```
init
init
1401231079432 1401231079432
```

① `__new__`: 

- `__init__`: 实例对象创建完成后被调用设置对象属性初始值。

- `__new__`: 创建实例然后返回该实例，是个静态方法。

② `super`:
- `super()` 函数是用于调用父类(超类)的一个方法。
- Python3.x 和 Python2.x 的一个区别是: Python 3 可以使用直接使用   `super().xxx` 代替 `super(Class, self).xxx` :

### 装饰器

```
from functools import wraps
def singleton(cls):
    _instance = {}
    @wraps(cls)       # ①
    def getinstance(*args, **kwargs):
        if cls not in _instance :
            _instance[cls] = cls(*args, **kwargs)
        return _instance[cls]
    return getinstance

@singleton
class Test():
    def __init__(self):
        print("init")
        
test1 = Test()
test2 = Test()
print(test1 == test2)
print(id(test1),id(test2))
```

输出：

```
init
True
1401229666696 1401229666696
```

① wraps的作用：`wraps`这个修饰器的作用就是将 被修饰的函数(wrapped) 的一些属性值赋值给修饰器函数(wrapper) ，最终让属性的显示更符合我们的直觉
https://segmentfault.com/a/1190000009398663
https://docs.python.org/3/library/functools.html#functools.wraps

### meteclass

```
class Singleton(type):
    _instances = {}
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super(Singleton, cls).__call__(*args, **kwargs)
        return cls._instances[cls]
    
# python3
class Test(metaclass=Singleton):
    def __init__(self):
        print("init")

# python2
# class Test():
#     __metaclass__ = Singleton
#     def __init__(self):
#         print("init")
        
test1 = Test()
test2 = Test()
print(id(test1),id(test2))
```

输出：

```
init
1401231008712 1401231008712
```

### 总结：

上面的四种方法中，使用`__new__`的方法来创建单例，init输出了两次，因为`__new__`是创建了一次实例，在第二次创建的时候检查到已经有实例了，就不会再创建了，但是却没有初始化实例，于是`__new__`被执行了一次，但每次创建对象，初始化实例对象属性的方法`__int__`会被调用两次。其他的方法中，实例的创建是先后调用了`__new__`,`__init__`即返回了一个初始化属性后的实例，再次检查实例的时候已经存在实例，也就不会再次创建了