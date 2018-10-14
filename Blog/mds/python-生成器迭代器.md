> &emsp;&emsp;

#### 迭代器
##### 迭代
**迭代是程序中对一组指令（或一定步骤）的重复。**
```Python
def fun():
    nums = [1,2,3,4]
    for i, e in enumerate(nums):
        nums[i] = e + 2
    print(nums)

fun()
# [3, 4, 5, 6]
```
上述函数fun()，每次取nums中的一个元素，并使该元素自加2，遍历完nums后，打印出nums。
这就是一个迭代的过程，这个过程包括遍历nums的所有元素，以及对每个元素进行自加2的操作。

##### 可迭代对象
    在python中，迭代大多是通过for...in...来完成的，能通过for...in...来遍历的对象，就是可迭代对象，可以通过collections模块的Iterable类型判断对象是否是可迭代对象。

