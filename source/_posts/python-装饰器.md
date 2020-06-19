---
title: Python装饰器
date: 2020-06-17 10:37:55
tags: [python]
---

&emsp;&emsp;装饰器(Decorator)可以在不修改函数的情况下，增强函数的功能，有助于公用逻辑的复用，增强程序的可读性，在python中有着举足轻重的地位。

## 1. 一切皆对象，函数也是对象
要想知道装饰器的原理，需要先知道python中函数也是对象，函数可以赋值给变量，函数可以作为返回值，函数可以作为参数传递。
<!-- more -->
```python
# 函数赋值给变量，通过变量调用
def func1():
    print("Hello World")
f1 = func1
f1()    # "Hello World"

# 函数作为返回值， 函数赋值给变量，通过变量调用
def func2():
    def inner():
        print("Hello World")
    return inner
f2 = func2()
f2()  # "Hello World"

# 函数作为参数，函数赋值给变量，通过变量调用
def func3():
    print("Hello World")

def wrapper(func):
    return func()

wrapper(func3)  # "Hello World"

```

## 2. 简单装饰器
有了上面的基础，现在我们给出一个简单的装饰器的例子
```python
def log1(func):
    def wrapper(*args, **kw):
        print("func: %s, input: args: %s, kw: %s"%(func.__name__, args, kw))
        result = func(*args, **kw)
        print("output: %s"%(result))
        return result
    return wrapper

def func1(a, b):
    return a + b

f1 = log1(func1)
f1(1, 2)
# func: func1, input: args: (1, 2), kw: {}
# output: 3
```
**装饰器log1接收一个函数func作为参数，同时返回内部函数wrapper。f1指向wrapper，调用f1(1, 2)时，wrapper接收f1的参数并在内部调用了func1，这样在不修改func1的基础上既实现func1功能，同时又为func1增加了格外的功能（打印了func1的输入输出记录）。**

其中f1 = log1(func1) 可借助@语法糖在函数定义处简写为
```python
@log1
def func1(a, b):
    return a + b
```

## 3. 带参数的装饰器
当装饰器需要传入参数时，需要在简单装饰器外层再包装一层，用来传递装饰器的参数
```python
def log2(name=None):
    def decorator(func):
        def wrapper(*args, **kw):
            logname = name if name else func.__name__
            print("name: %s, input: args: %s, kw: %s"%(logname, args, kw))
            result = func(*args, **kw)
            print("output: %s"%(result))
            return result
        return wrapper
    return decorator

@log2("sum")
def func2(a, b):
    return a + b

func2(1, 2)
# name: sum, input: args: (1, 2), kw: {}
# output: 3

# 这种3层嵌套相当于func2 = log2("sum")(func2)
```
带参数的装饰器同样支持默认参数，当使用默认参数时，应写为@log2()，而不是@log2

## 4. 保留函数元信息
>&gt;&gt;&gt; print(func1.\_\_name\_\_)
>wrapper   
>&gt;&gt;&gt; print(func2.\_\_name\_\_)
>wrapper   

打印被装饰函数的\_\_name\_\_属性，发现不再是原来的函数名，函数的元信息已丢失，这将导致某些依靠函数元信息的代码执行出错。为了解决这个问题，需要使用python内置装饰器functools.wraps来注解底层包装函数，完整的代码如下：
```python
import functools

def log1(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print("func: %s, input: args: %s, kw: %s"%(func.__name__, args, kw))
        result = func(*args, **kw)
        print("output: %s"%(result))
        return result
    return wrapper

def log2(name=None):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            logname = name if name else func.__name__
            print("name: %s, input: args: %s, kw: %s"%(logname, args, kw))
            result = func(*args, **kw)
            print("output: %s"%(result))
            return result
        return wrapper
    return decorator

@log1
def func1(a, b):
    return a + b

@log2("sum")
def func2(a, b):
    return a + b

func1(1, 2)
# func: func1, input: args: (1, 2), kw: {}
# output: 3

func2(1, 2)
# name: sum, input: args: (1, 2), kw: {}
# output: 3

print(func1.__name__)   # func1
print(func2.__name__)   # func2
```

## 5. 类装饰器
类的\_\_call\_\_方法可以让实例像函数一样被调用，这样我们可以将装饰器的逻辑放在\_\_call\_\_方法内部实现，这样做的好处是可以使用类的特性，例如继承等。
```python
import functools

class BaseDecorator(object):
    def __call__(self, func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            self.doSomeThings()
            return func(*args, **kwargs)
        return wrapper

    def doSomeThings(self):
        pass

class ChildDecorator(BaseDecorator):
    def doSomeThings(self):
        print("Hello World")

@ChildDecorator()
def func1():
    pass

func1()
# Hello World
```

## 6. 多个装饰器调用顺序
```python
@a
@b
@c
def func():
    pass
```
当一个函数被多个装饰器修饰时，执行顺序是从下往上执行，上述例子等价于 func = a(b(c(func)))。


