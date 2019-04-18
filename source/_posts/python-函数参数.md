> &emsp;&emsp;Python函数定义时参数灵活，使用不同参数的组合不仅可以简化调用者的代码，还可以处理复杂的参数。
函数的参数除了有必选参数外，还可以使用默认参数，可变参数，关键字参数和命名关键字参数。

#### 位置参数
定义一个计算x^2的函数，以及一个计算x^n的函数
```python
def calc1(x):
    return x * x

def calc2(x, n):
    s = 1
    for i in range(n):
        s *= x
    return s
```
对于这两个函数，其参数都是位置参数，同时也是必选参数，调用函数时实参需和形参一一对应，当参数不对应时会引起错误，例如：
> &gt;&gt;&gt;calc2(2)    
> Traceback (most recent call last):   
> File "&lt;stdin&gt;", line 1, in &lt;module&gt;     
> TypeError: calc2() takes exactly 2 arguments (1 given)    
>     
> &gt;&gt;&gt;calc2(2, 2, 2)    
> Traceback (most recent call last):   
> File "&lt;stdin&gt;", line 1, in &lt;module&gt;      
> TypeError: calc2() takes exactly 2 arguments (3 given)     

#### 默认参数
对于函数calc2，如若函数调用过程中，计算x^2使用的较多，每次调用都要通过calc2(x, 2)来调用，这样就略显繁琐，此时可以通过默认参数来简化函数的调用，改写函数为：
```python
def calc3(x, n = 2):
    s = 1
    for i in range(n):
        s *= x
    return s
```
这样，我们在调用calc3(2)时，就相当于调用calc3(2, 2)，而对于其他次方仍需明确指出n。
默认参数在使用中应当的注意：位置(必选)参数在前，默认参数在后。
    
当有多个默认参数时，既可以按顺序提供默认参数，也可以不按顺序提供默认参数。
```python
def saveInfo(name, gender, age = 23, city = 'HangZhou'):
    print(name, gender, age, city)

saveInfo('Alen', 'M', city='Beijing')
```
    
定义默认参数时需要特别注意的一点：**默认参数必须指向不变对象！**
看如下例子：
```python
def addEnd(L=[]):
    L.append('END')
    return L
```
> &gt;&gt;&gt;addEnd()   
> ['END']    
> &gt;&gt;&gt;addEnd()  
> ['END', 'END']    
> &gt;&gt;&gt;addEnd()    
> ['END', 'END', 'END']     

出现这样的结果是因为：默认参数L仅仅代表[]的一个引用，函数定义时[]就已确定，每次调用addEnd时，使用的都是函数定义时的[]，通过print(id(L))可以看出，每次调用该函数时使用的是同一内存地址的[]。
```python
def addEnd(L=[]):
    L.append('END')
    print(id(L))
    return L

addEnd()
#4518465224
#['END']
addEnd()
#4518465224
#['END', 'END']
```
为了避免这个问题，可以通过下面的方式，每次调用函数时，都会新建一个[]：
```python
def addEnd(L=None):
    L = [] if L == None else L
    L.append('END')
    print(id(L))
    return L

addEnd()
#4518687688
#['END']
addEnd()
#4518688328
#['END']
```
#### 可变参数
可变参数即传入的参数个数是可变的，可以是0个，1个，或多个   
先定义一下函数，给定一组数字a，b，c...，计算这组数字的平方和a^2 + b^2 + c^2 + ...
```python
def calc(numbers):
    s = 0
    for num in numbers:
        s = s + num * num
    return s
```
该函数在调用时，需先组装一个list或tuple    
> &gt;&gt;&gt;calc([1, 2, 3])     
> 14
> &gt;&gt;&gt;calc([1, 2, 3])     
> 30

如果利用可变参数，函数的调用可以简化
```python
def calc(*numbers):
    s = 0
    for num in numbers:
        s = s + num * num
    return s

calc()
# 0
calc(1, 2, 3)     
# 14
calc(1, 2, 3, 4)
# 30
```
可变参数与list或tuple参数相比，仅仅在参数前面加了个*号，**\*的作用是将传入的参数组装成一个tuple**
，如果已经有一个list或tuple，要调用可变参数怎么办呢？
> &gt;&gt;&gt;nums = [1, 2, 3]     
> &gt;&gt;&gt;calc(nums[0], nums[1], nums[2])     
> 14  

这种写法就过于繁琐，针对这种情况，python允许在list或tuple前加一个*号，将list或tuple的元素变成可变参数传入
> &gt;&gt;&gt;nums = [1, 2, 3]     
> &gt;&gt;&gt;calc(*nums)       
> 14    

#### 关键字参数
关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict    
定义一个用户注册的函数
```Python
def user(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)

user('Bob', 23)          
# ('name:', 'Bob', 'age:', 23, 'other:', {})
user('Alen', 20, gender='M', job='Engineer') 
# ('name:', 'Alen', 'age:', 20, 'other:', {'gender': 'M', 'job': 'Engineer'})
```
函数user除了接收必选参数name，age外还接收关键字参数kw，调用该函数时，可以只传入必选参数，也可以传入任意个数的关键字参数，**\*\*的作用是将含参数名的参数，组装成一个dict传入kw**。关键字参数可以扩展函数的功能，调用者如果提供更多的信息，我们仍可以接收到。例如当用户注册时，姓名和年龄时必填项，其他是可选项，就可以通过关键字参数来实现。        
和可变参数类似，当有现成dict时，可通过以下方式简化调用：
> &gt;&gt;&gt;extra = {'city': 'Beijing', 'job': 'Engineer'}    
> user('Bob', 23, **extra)    
> name: Bob age: 23 other: {'city': 'Beijing', 'job': 'Engineer'}     

\*\*extra表示把extra这个dict的所有key-value用关键字参数传入到函数的\*\*kw参数，kw将获得一个dict。**注意kw获得的dict是extra的一份拷贝，对kw的改动不会影响到函数外的extra。**
#### 命名关键字参数
对于关键字参数，函数的调用者可以传入任意不受限制的关键字参数，如果要限制关键字参数的名字，就可以用命名关键字参数。命名关键字参数需要一个特殊分隔符\*，\*后面的参数被视为命名关键字参数，例如，只接收city和job作为关键字参数：
```Python
def user(name, age, *, city, job):
    print('name:', name, 'age:', age, 'city:', city, 'job:', job)

user('Alen', 20, city='Beijing', job='Engineer') 
# name: Alen age: 20 city: Beijing job: Engineer
```
不同于位置参数，命名关键字参数必须传入参数名。如果没有传入参数名，调用将报错：
> &gt;&gt;&gt;user('Alen', 20, 'Beijing', 'Engineer')    
> Traceback (most recent call last):    
>   File "&lt;stdin&gt;", line 1, in &lt;module&gt;    
> TypeError: user() takes 2 positional arguments but 4 were given

若函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符*了：
```Python
def user(name, age, *args, city, job):
    print(name, age, args, city, job)
```
命名关键字参数可以有缺省值，从而简化调用：
```Python
def user(name, age, *, city='Beijing', job):
    print(name, age, city, job)

user('Bob', 22, job='Engineer')
# Bob 22 Beijing Engineer
```
#### 参数组合
定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。但是应注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。
定义一个函数，包含上述参数：
```Python
def user(name, age = 20, *args, city = 'Beijing', job, **kw):
    print(name, age, args, city, job, kw)

user('Bob', 20, 120, job = 'Engineer', gender = 'M')
# Bob 20 (120,) Beijing Engineer {'gender': 'M'}
```
对于任意函数，都可以通过类似func(*args, **kw)的形式调用它，无论它的参数是如何定义的。
> &gt;&gt;&gt;args = ('Bob', 20, 120)     
> &gt;&gt;&gt;kw = {'job':'Engineer', 'gender':'M'}     
> &gt;&gt;&gt;user(*args, **kw)     
> Bob 20 (120,) Beijing Engineer {'gender': 'M'}