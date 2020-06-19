---
title: Python迭代器生成器
date: 2018-10-13 22:12:02
tags: [python]
---

&emsp;&emsp;在学习python数据结构的过程中，可迭代对象，迭代器，生成器这些概念参杂在一起，难免让初学者一头雾水，今天就来捋捋这些概览。

## 1. 可迭代对象(iterable)
什么是可迭代对象，通俗的讲就是可以直接通过for循环遍历的对象就可称为可迭代对象Iterable，可以使用isinstance()判断一个对象是否是Iterable对象：
<!-- more -->
>&gt;&gt;&gt;from collections import Iterable  
>&gt;&gt;&gt;isinstance([], Iterable)  
>True  
>&gt;&gt;&gt;isinstance({}, Iterable)  
>True   
>&gt;&gt;&gt;isinstance('123', Iterable)   
>True  
>&gt;&gt;&gt;isinstance(123, Iterable)   
>False   

可迭代对象并不指某种具体的数据类型，list, dict, set, str都是迭代对象，再比如打开状态的files，sockets也是可迭代对象，可迭代对象是指代对象的一种属性，代表该对象是可迭代的。可迭代对象实现了\_\_iter\_\_方法，该方法返回一个迭代器对象。

## 2. 迭代器(iterator)
任何实现了\_\_iter\_\_和\_\_next\_\_方法的对象都是迭代器（python2是实现\_\_iter\_\_和next方法），\_\_iter\_\_返回迭代器自身，\_\_next\_\_返回容器中的下一个值，如果容器中没有更多元素了，则抛出StopIteration异常。可以使用isinstance()判断一个对象是否是Iterator对象：
>&gt;&gt;&gt;from collections import Iterator  
>&gt;&gt;&gt;isinstance([], Iterator)  
>False  
>&gt;&gt;&gt;isinstance({}, Iterator)  
>False   
>&gt;&gt;&gt;isinstance('123', Iterator)   
>False  
>&gt;&gt;&gt;isinstance((x for x in range(10)), Iterator)    
>True  

其中(x for x in range(10))是生成器表达式，它返回的是一个生成器对象，不同于列表生成式[x for x in range(10)]返回一个list对象。生成器对象都是迭代器对象，但list, dict, str虽然是可迭代对象，但不是迭代器对象，可以使用iter()将list, dict, str等可迭代对象变成迭代器对象。

>&gt;&gt;&gt;isinstance(iter([]), Iterator)  
>True   
>&gt;&gt;&gt;isinstance(iter('123'), Iterator)  
>True   

python的迭代器对象表示一个数据流，可以将这个数据流看作一个有序序列，但我们并不知道序列的长度，只能不断通过调用next()函数实现按需计算下一个数据，因此迭代器的计算是惰性的，只有在需要返回下一个数据时它才计算，迭代器的这种特性可以大大减少内存的开销，迭代器对象甚至可以表示一个无限大的数据流，而让list, dict或者str存储一个无限大的数据流是不可能的。

下面我们通过迭代器来实现斐波那契数列：
```python
from collections import Iterable
from collections import Iterator

class Fib:
    def __init__(self, max):
        self.n, self.max = 0, max
        self.a, self.b = 0, 1

    def __iter__(self):
        return self

    def __next__(self):
        if self.n < self.max:
            self.n += 1
            self.a, self.b = self.b, self.a + self.b
            return self.a
        else:
            raise StopIteration

if __name__ == '__main__':
    fib = Fib(10)
    print(isinstance(fib, Iterable)) # True
    print(isinstance(fib, Iterator)) # True
    print([e for e in fib]) # [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
```

Fib既是一个可迭代对象（因为它实现了\_\_iter\_\_方法），又是一个迭代器（因为实现了\_\_next\_\_方法）。实例变量a和b用于维护迭代器内部的状态。每次调用next()方法的时候做两件事：
为下一次调用next()方法修改状态，为当前这次调用生成返回结果。    

**迭代器就像一个懒加载的工厂，等到有人需要的时候才给它生成值返回，没调用的时候就处于休眠状态等待下一次调用。**


## 3. 生成器(generator)
生成器是一种特殊的迭代器，不过这种迭代器更加优雅。它不需要再像上面的类一样写\_\_iter\_\_()和\_\_next\_\_()方法了，只需要一个yiled关键字。 

用生成器来实现斐波那契数列的例子是：
```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        n = n + 1
        a, b = b, b + a
        yield a

f = fib(10)
print(f) # <generator object fib at 0x10d8cf888>
print([e for e in f])   # [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
```
fib函数中的yield关键字，将该函数变成了一个生成器，当执行f=fib(10)返回的是一个生成器对象，此时函数中的代码并不会执行，只有显示或隐示地调用next的时候才会真正执行里面的代码，在每次调用next()方法时，遇到yield语句返回值并中断，再次执行时从上次返回的yield语句处继续执行。
生成器是python非常强大的特性，相比其他容器对象它更加节省内存，同时使用更少的代码，使你的代码更加的优雅，凡事以下结构都可以通过生成器重构：
```python
def fun():
    result = []
    for ... in ...:
        result.append(x)
    return result

def fun_gen():
    for ... in ...:
        yield x
```

## 4. 总结
1. 可迭代对象实现了\_\_iter\_\_方法，该方法返回一个迭代器对象。
2. 迭代器持有一个内部状态的字段，用于记录下次迭代返回值，它实现了\_\_next\_\_和\_\_iter\_\_方法，迭代器不会一次性把所有元素加载到内存，而是需要的时候才生成返回结果。
3. 生成器是一种特殊的迭代器，它的返回值不是通过return而是用yield。
