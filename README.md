# python后端开发面试常见问题

>>&emsp; 大家好，我叫亓官劼（qí guān jié ），在GitHub和CSDN中记录学习的点滴历程，时光荏苒，未来可期，一起加油~~
>
>本篇文章将在GitHub和CSDN上持续更新，主要是Python后端开发的一些常见问题，包括Python的一些基础知识，以及面试中常问的计网，数据库，数据结构等一些算法题，总体覆盖面试的大多数问题。这里分为Python基础、计算机网络相关、数据库相关、Linux相关、数据结构与算法和其他共六部分进行整理。
>
>> 本文的GitHub地址为：[python-development-interview-FAQ](https://github.com/qiguanjie/python-development-interview-FAQ)
>> CSDN地址为：[python后端开发面试常见问题 (持续更新)](https://qiguanjie.blog.csdn.net/article/details/114789666)
>>>
>> > 微信公众号为：【亓官劼】
>
>
>> 如果有帮助的话，可以在GitHub上点个star，支持下。相对来说GitHub更新要比CSDN上更新更快一些。
>
>创建了个交流群，如果需要可以加群一起交流，Q群545611263，也可以加我V：qiguanjie2015

---


[TOC]



# Python 基础

## 0 说明占位符

* 本文中不特别标注Python版本的，全部默认为Python3

## 1 Python类中的方法类型

在Python类中有四种方法类型，分别是实例方法、静态方法、类方法和普通方法。

* 实例方法(即对象方法)：需要实例化对象之后才能调用，接受的第一个参数`self`就是对象本身，必须使用实例化对象才可以访问，不能通过类直接访问.
* 静态方法：可以通过类名直接调用，不需要传递`self`和`cls`；也可以在实例化对象后调用
* 类方法：可以通过类名调用，也可以在实例化对象后调用。类方法需要一个`cls`参数，在调用时自动传递
* 普通方法：和正常的函数一样，可以直接调用，但是在类中不建议写这种方法

测试示例：

```python
class A(object):
    # 实例方法(对象方法),需要接收一个形参self，即实例对象自己
    def instance_method_fun(self):
        print("instance_method_fun,self is {}".format(self))
    
    # 类方法,需要接收一个形参cls，在调用时自动传递
    @classmethod
    def classmethod_fun(cls):
        print("classmethod_fun, cls is {}".format(cls))

    # 静态方法
    @staticmethod
    def staticmethod_fun():
        print("staticmethod_fun")

    # 普通方法
    def common_fun():
        print("common_fun")

# A.instance_method_fun() # 报错：TypeError: instance_method_fun() missing 1 required positional argument: 'self'
A.classmethod_fun() # 输出：classmethod_fun, cls is <class '__main__.A'>
A.staticmethod_fun() # 输出：staticmethod_fun
A.common_fun() # 输出：common_fun  （不建议在类中写普通方法）


a = A()
a.instance_method_fun() # 输出：instance_method_fun,self is <__main__.A object at 0x0000018674E1E588>
a.classmethod_fun()# 输出：classmethod_fun, cls is <class '__main__.A'>
a.staticmethod_fun()# 输出：staticmethod_fun
```

## 2 Python的参数传递类型

Python中的参数传递是引用传递，即我们可以对传递对象的属性，但是不能改变传递对象的指针。在Python中有一些对象的值是不可以更改的，例如int，float类型。如果在函数体内修改了不可修改对象的值，Python会在一个新的内存地址上创建一个变量，而不是使用原来的变量；如果在函数体内修改一个可修改对象的值，则在原内存地址操作。

例如：

```python
def fun1(x):
    x = x + 1
    print("x:{},id(x):{}".format(x,id(x)))

def fun2(x):
    print("b:{},id(b):{}".format(b,id(b)))
    x.append(2)
    print("b:{},id(b):{}".format(b,id(b)))

a = 1
print("a:{},id(a):{}".format(a,id(a)))
fun1(a)
print("a:{},id(a):{}".format(a,id(a)))

b = []
print("b:{},id(b):{}".format(b,id(b)))
fun2(b)
print("b:{},id(b):{}".format(b,id(b)))

# 输出为：
# a:1,id(a):1860272240
# x:2,id(x):1860272272
# a:1,id(a):1860272240
# b:[],id(b):2262818473288
# b:[],id(b):2262818473288
# b:[2],id(b):2262818473288
# b:[2],id(b):2262818473288
```

## 3 协程

这个是在前不久的面试中才知道有协程这个概念，其实也可以理解为用户线程，相比较内核线程而言，用户线程更加的灵活，并且减少了进出内核态的消耗，缺点是无法利用多核CPU的并行优势。

## 4 Python命名中的单下划线(_)和双下划线(__)

在Python中，双下划线开头和结尾的命名默认为Python的内部变量/方法，用以区分用户变量。例如场景的`__init__()`,`__dict__`,`__dir__`等。

单下划线开头的命名默认为私有变量，不会在`from a import *`中被导入

双下划线开头，但是没有下划线结尾的命名，Python在解释的时候会默认对其进行重命名为`_类名__变量`。

```python
class A():
    def __init__(self) -> None:
        self._b = "self._b"
        self.__c = "self.__c"
a = A()
print(a._b) # 输出：self._b
# print(a.__c) # 报错：AttributeError: 'A' object has no attribute '__c'
print(a.__dict__) # 输出：{'_b': 'self._b', '_A__c': 'self.__c'}
# 我们发现__c变量被自动重命名为_A__c了
print(a._A__c) # 输出：self.__c
```

在Python中，当一个文件夹下有一个`__init__.py`文件，则Python会识别这个文件夹为一个Python包

## 5 python字符串传参 %s和format

%s和format的区别在于，format可以传递列表、元组等类型，而%s不可以传递元组类型（%s可以传递列表类型），所以在日常使用时，使用format更加方便



## 6 python 迭代器和生成器

迭代器是python十分强大的一个功能，迭代器是一个可以记住遍历位置的对象。例如：

```python
a = [1,2,3,4]
it = iter(a) # 获取一个迭代器对象
print(next(it)) # 遍历下一个位置，输出：1
print(next(it)) # 遍历下一个位置，输出：2
```

我们也可以使用迭代器来生成一个我们需要的列表，例如：

```python
a = [i*i for i in range(1,9)] # 使用迭代器生成一个[1,9)的平方的列表
print(a) # 输出：[1, 4, 9, 16, 25, 36, 49, 64]
```

在这里如果我们将外面的`[]`改为`()`，a获取的将会是一个生成器，而不是迭代器。在python中，使用yield的函数被称为生成器，生成器返回的是一个迭代器的函数，只能用于迭代操作，在调用生成器运行的过程中，每次遇到 yield 时函数会暂停并保存当前所有的运行信息，返回 yield 的值, 并在下一次执行 next() 方法时从当前位置继续运行。

生成器不保留所有的数据信息，而是指记录当前运行的信息。因此生成器就不能直接获取某个下表的值，而是要通过`next()`或者`sent()`，亦或是迭代器来依次获取值。

例如：

```python
a = (i*i for i in range(1,9)) # 使用生成器生成一个[1,9)的平方的列表
print(a) # 输出：[1, 4, 9, 16, 25, 36, 49, 64]
# print(a[2]) # 报错TypeError: 'generator' object is not subscriptable
it = iter(a) # 获取一个迭代器对象
print(next(a)) # 输出 1
print(next(a)) # 输出 4
print(next(a)) # 输出 9
print(next(a)) # 输出 16
print("=================")
for item in a:
    print(item)
# 输出：
# 25
# 36
# 49
# 64
```



生成器常用场景：例如我们需要生成一个有规律向前推进的列表，或者一个从1到1亿的列表等等，当我们列表中元素数量十分大时，内存会爆栈。但是如何我们元素间是有规律的，则我们可以利用生成器来解决这个问题。

有需要的话可以参考这篇文章：[学点简单的Python之Python生成器](https://qiguanjie.blog.csdn.net/article/details/112368651)

## 7 python 装饰器

装饰器是一个十分常用的东西，经常被用在有切面需求的场景，大家耳熟能详的AOP就是这个，装饰器的主要功能就是为已经存在的函数提供一些可复用的定制化功能。

装饰器包含很多内容，需要系统的去看，这里不展开。如有需要，可以参考这篇文章：[学点简单的Python之Python装饰器与闭包](https://qiguanjie.blog.csdn.net/article/details/112415573)

## 8 python 变量中的作用域

python中变量总是默认本地变量，如果没有，则会创建一个本地变量。在函数中如果要使用全局变量，需要使用`gloabl`进行声明。例如：

```python
a = 5
def fun():
    global a
    a = a + 1
fun()
print(a)# 输出：6
```

## 9 python 闭包

python闭包与其他语言的闭包的意思是一样的，即我们在函数定义中引用了函数外定义的变量，并且该函数可以在其定义环境外被执行。简单来说，就是在函数内定义函数，且函数内部定义的函数中使用了外部函数中的变量，且内部函数可以单独的运行。

这里用语言来描述还是比较的绕，我们可以通过下面这个小例子来更直观的理解：

```python
# 定义一个函数extern_func
def extern_func():
    # 在extern_func函数内定义一个空列表list
    list = []
    # 在extern_func函数内定义一个函数inner_func
    def inner_func(name):
        # inner_func函数的功能是在list列表中添加一个name,然后输出list
        list.append(name)
        print(list)
    # exten_func函数的返回值是inner_func的函数体
    return inner_func

# 调用extern_func函数，返回值赋值给ret1，即ret1为inner_func函数体
ret1 = extern_func()
ret1('zhangsan')# 调用ret1,在list中添加一个'zhangsan',并输出list
ret1('lisi')# 调用ret1,在list中添加一个'lisi',并输出list

# 调用extern_func函数，返回值赋值给ret2，即ret2为inner_func函数体
ret2 = extern_func()
ret2('wangwu')# 调用ret2,在list中添加一个'wangwu',并输出list

ret1('qiguanjie')# 调用ret1,在list中添加一个'lisi',并输出list
```

输出为：

```python
['zhangsan']
['zhangsan', 'lisi']
['wangwu']
['zhangsan', 'lisi', 'qiguanjie']
```

我们发现`ret1`和`ret2`中虽然都是在`list`中添加一个`name`并返回，但是`ret1`和`ret2`是两次调用`extern_func( )`返回的函数体，他们作用的list是不同的，他们作用的`list`可以脱离原函数`extern_func()`而单独使用。这就是函数闭包的简单使用。简而言之就是我们在函数中定义函数，并且使用了外部函数中的部分变量，我们内部的函数在脱离外部函数之后继续执行，且单独作用于外部函数的部分变量。

### 闭包一个常见错误:

我们看下面这个例子

```python
def func():
    list = []
    for i in range(3):
        def inner_func():
            return i*i
        list.append(inner_func)
    return list
re1,re2,re3= func()
print(re1())
print(re2())
print(re3())
```

大家是不是以为三个输出的结果应该是`0,1,4`?但实际上输出的结果都是`4`，这是为什么呢？这里我们的i对于`inner_func`来说是一个外部函数，我们在`list`中添加是是`inner_func`的函数体，里面返回的是`i*i`，但是当我们i变化到2之后，我们才返回list，所以我们输出的三个值才都是`4`，那如何避免这种情况呢？

#### 第一种方法，区分变量

我们使用`_i`来区分`i`

```python
def func():
    list = []
    for i in range(3):
        def inner_func(_i = i):
            return _i*_i
        list.append(inner_func)
    return list
re1,re2,re3= func()
print(re1())
print(re2())
print(re3())
```

这里我们在`inner_func`的参数中定义`_i`变量，值为当前的`i`，这时我们将函数体加入`list`中，就可以保证值不受`i`值变化的影响，输出为0，1，4。另一种方法就是我们直接在`list`中存放计算好的值，而不是函数体（这种方法有一定的局限性，和之前的方法就是两种完全不同的方法了）：

```python
def func():
    list = []
    for i in range(3):
        def inner_func():
            return i*i
        list.append(inner_func())
    return list
re1,re2,re3= func()
print(re1())
print(re2())
print(re3())
123456789101112
```

这里我们这里添加到list中的是`inner_func()`，一旦有`()`则表明我们这个函数已经运行了，返回的是一个值。

#### 第二种方法 nonlocal关键字

那如果我们要在内部函数中使用外部函数中的变量，并进行修改我们应该如何做呢？

```python
def extern_func(name):
    print('exter_func name is : %s' % name)
    def inner_func():
        name = 'inner_func ' + name
        print('inner_func name is : %s' % name)
    return inner_func

ret = extern_func('qiguanjie')()
```

如何我们直接修改的话，我们会发现这里编译会报错：`UnboundLocalError: local variable 'name' referenced before assignment`

这里报错的意思即我们这里的`name`变量在使用前未被分配，这就和我们在函数内使用全局变量时要使用`globle`关键字一样，这里在内部函数中要使用并更改外部函数中的变量，我们需要使用关键字`nonlocal`，对程序进行修改：

```python
def extern_func(name):
    print('exter_func name is : %s' % name)
    def inner_func():
        nonlocal name
        name = 'inner_func ' + name
        print('inner_func name is : %s' % name)
    return inner_func

ret = extern_func('qiguanjie')()
```

此时程序顺利编译，输出结果为：

```python
exter_func name is : qiguanjie
inner_func name is : inner_func qiguanjie
```

## 10 python lambda函数

lambda函数即匿名函数，在很多场合都可以使用。lambda 函数比较轻便，即用即仍，很适合需要完成一项功能，但是此功能只在此一处使用，连名字都很随意的情况下。

例如在sort函数中指定排序的`key`:

```python
a = [{"a":13,"b":25,"c":62},{"a":63,"b":215,"c":612},{"a":3,"b":634,"c":216}]
a.sort(key=lambda x: x['a'])
print(a) # 输出： [{'a': 3, 'b': 634, 'c': 216}, {'a': 13, 'b': 25, 'c': 62}, {'a': 63, 'b': 215, 'c': 612}]
a.sort(key=lambda x: x['b'])
print(a) # 输出： [{'a': 13, 'b': 25, 'c': 62}, {'a': 63, 'b': 215, 'c': 612}, {'a': 3, 'b': 634, 'c': 216}]
a.sort(key=lambda x: x['c'])
print(a) # 输出： [{'a': 13, 'b': 25, 'c': 62}, {'a': 3, 'b': 634, 'c': 216}, {'a': 63, 'b': 215, 'c': 612}]

```

## 11 python中的深拷贝与浅拷贝

在浅拷贝时，拷贝出来的新对象的地址和原对象是不一样的，但是新对象里面的可变元素（如列表）的地址和原对象里的可变元素的地址是相同的，也就是说浅拷贝它拷贝的是浅层次的数据结构（不可变元素），对象里的可变元素作为深层次的数据结构并没有被拷贝到新地址里面去，而是和原对象里的可变元素指向同一个地址，所以在新对象或原对象里对这个可变元素做修改时，两个对象是同时改变的，但是深拷贝不会这样，这个是浅拷贝相对于深拷贝最根本的区别。

在深拷贝时，会只拷贝所有元素的值，包括可变对象，也仅拷贝对象中的值，而不是地址。

```python
import copy
a = [1,2,3,4,['a','b','c']]
b = a # 赋值（引用传递）
c = copy.copy(a)# 浅拷贝
d = copy.deepcopy(a) # 深拷贝

a.append(5)
a[4].append('d')
print("a:{},  id(a):{}, id(a[4]):{}".format(a,id(a),id(a[4])))
print("b:{},  id(b):{}, id(b[4]):{}".format(b,id(b),id(b[4])))
print("c:{},     id(c):{}, id(c[4]):{}".format(c,id(c),id(c[4])))
print("d:{},          id(d):{}, id(d[4]):{}".format(d,id(d),id(d[4])))

# 输出为：
# a:[1, 2, 3, 4, ['a', 'b', 'c', 'd'], 5],  id(a):1998224934024, id(a[4]):1998224933896
# b:[1, 2, 3, 4, ['a', 'b', 'c', 'd'], 5],  id(b):1998224934024, id(b[4]):1998224933896
# c:[1, 2, 3, 4, ['a', 'b', 'c', 'd']],     id(c):1998224936904, id(c[4]):1998224933896
# d:[1, 2, 3, 4, ['a', 'b', 'c']],          id(d):1998224935752, id(d[4]):1998224957960
```

## 12 Python中*args和**kwargs

`*args`表示传一个元组给函数，可以同时传递多个参数，这里的`args`可以替换为其他名称，前面加一个`*`即可，例如：`*para`都可以。

`**kwargs`表示传一个字典给函数，可以传多个键值对，这里的`kwargs`同样也可以替换为其他名称，前面有`**`即可。

两者的不同如下所示：

```python
def fun(*args,**kwargs):
  print("args: ",args)
  print("kwargs: ",kwargs)

fun("hello","world","!","this","is","args",a=1,b=2,c=3)

# 输出为：
# args:  ('hello', 'world', '!', 'this', 'is', 'args')
# kwargs:  {'a': 1, 'b': 2, 'c': 3}
```

## 13 Python中`__new__`和`__init__`的区别

`__new__`是静态方法，会返回一个创建的实例

`__init__`是实例方法，无返回值

## 14 Python中的单例模式

单例模式是一种特别重要的设计模式，通过单例模式可以保证系统中一个类只有一个实例并且该实例易于被外界访问，方便控制实例个数并节约系统资源。实现单例模式的常用方法如下：

### 1 通过`import`导入

在Python中使用`import`来导入一个对象，则是天然的单例模式。因为模块在第一次导入时，会生成 `.pyc` 文件，当第二次导入时，就会直接加载 `.pyc` 文件，而不会再次执行模块代码。因此，我们只需把相关的函数和数据定义在一个模块中，就可以获得一个单例对象了。例如：

```python
# a.py中
class A(object):
    def fun(self):
        pass
test_a = A

# b.py中
from a import test_a
```



### 2 使用装饰器

```python
def Singleton(cls):
    _state = {}
    def _singleton(*args, **kargs):
        if cls not in _state:
            _state[cls] = cls(*args, **kargs)
        return _state[cls]
    return _singleton


@Singleton
class A(object):
    a = 1
    def __init__(self, x=0):
        self.x = x

a1 = A()
a2 = A()

print("id(a1): ",id(a1))
print("id(a2): ",id(a2))

print("a1.a: ",a1.a)
print("a2.a: ",a2.a)

a1.a = 2
print("a1.a: ",a1.a)
a2.a = 3
print("a2.a: ",a2.a)

print("a1.a: ",a1.a)

# 输出为：
# id(a1):  2973113231736
# id(a2):  2973113231736
# a1.a:  1
# a2.a:  1
# a1.a:  2
# a2.a:  3
# a1.a:  3
```



### 3 使用类实现

使用类实现的时候需要加锁，否则在多线程中无法保证单实例

```python
import time
import threading
class Singleton(object):
    _instance_lock = threading.Lock()

    def __init__(self):
        time.sleep(1)

    @classmethod
    def instance(cls, *args, **kwargs):
        with Singleton._instance_lock:
            if not hasattr(Singleton, "_instance"):
                Singleton._instance = Singleton(*args, **kwargs)
        return Singleton._instance


def task(arg):
    obj = Singleton.instance()
    print(obj)
for i in range(10):
    t = threading.Thread(target=task,args=[i,])
    t.start()
time.sleep(20)
obj = Singleton.instance()
print(obj)

# 输出：
# <__main__.Singleton object at 0x0000022E7C561E80>
# <__main__.Singleton object at 0x0000022E7C561E80>
# <__main__.Singleton object at 0x0000022E7C561E80>
# <__main__.Singleton object at 0x0000022E7C561E80>
# <__main__.Singleton object at 0x0000022E7C561E80>
# <__main__.Singleton object at 0x0000022E7C561E80>
# <__main__.Singleton object at 0x0000022E7C561E80>
# <__main__.Singleton object at 0x0000022E7C561E80>
# <__main__.Singleton object at 0x0000022E7C561E80>
# <__main__.Singleton object at 0x0000022E7C561E80>
```



### 4 基于`__new__`方法实现

当我们实例化一个对象时，是**先执行了类的__new__方法**（我们没写时，默认调用object.__new__），**实例化对象**；然后**再执行类的__init__方法**，对这个对象进行初始化，所有我们可以基于这个，实现单例模式

```python
import threading
class Singleton(object):
    _instance_lock = threading.Lock()

    def __init__(self):
        pass

    def __new__(cls, *args, **kwargs):
        if not hasattr(Singleton, "_instance"):
            with Singleton._instance_lock:
                if not hasattr(Singleton, "_instance"):
                    Singleton._instance = object.__new__(cls)  
        return Singleton._instance

obj1 = Singleton()
obj2 = Singleton()
print(obj1)
print(obj2)

def task(arg):
    obj = Singleton()
    print(obj)

for i in range(10):
    t = threading.Thread(target=task,args=[i,])
    t.start()

# 输出
# <__main__.Singleton object at 0x0000017B102EA978>
# <__main__.Singleton object at 0x0000017B102EA978>
# <__main__.Singleton object at 0x0000017B102EA978>
# <__main__.Singleton object at 0x0000017B102EA978>
# <__main__.Singleton object at 0x0000017B102EA978>
# <__main__.Singleton object at 0x0000017B102EA978>
# <__main__.Singleton object at 0x0000017B102EA978>
# <__main__.Singleton object at 0x0000017B102EA978>
# <__main__.Singleton object at 0x0000017B102EA978>
# <__main__.Singleton object at 0x0000017B102EA978>
# <__main__.Singleton object at 0x0000017B102EA978>
# <__main__.Singleton object at 0x0000017B102EA978>
```

## 15 Python中`is`和`==`的区别

`is`是对指针的比较，`==`是对值的比较。

```python
a = [1,2,3,4]
b = [1,2,3,4]
print(a is b) # 输出 False
print(a == b) # 输出 True
```

## 16 Python3 和Python2的区别

* Python2的print不用加`()`而python3需要
* python2的`/`表示整除(不加.0的时候)，而Python3是除
* Python3的`A / B`的返回值类型都是`float`，而Python2可能是整型
* python2默认编码是`ascii`，python3的默认编码是`utf-8`
* python3新增了nonlocal关键字，用于实现非局部变量

等等，这里例举的几个是常见的价格区别。

## 17 Python中a += X和a = a + x的区别

在Python中，`a += X和a = a + x`的实现机制是不同的，这里分为四种情况。

* 可变类型
  * `a += x`：将会在`a`原地址上进行修改，`a`的地址不变
  * `a = a + x`：将会新创建一个对象，名称为a，但是地址与原来的地址不同。
* 不可变类型
  * `a += x`：将会新创建一个对象，名称为a，但是地址与原来的地址不同。
  * `a = a + x`：将会新创建一个对象，名称为a，但是地址与原来的地址不同。

程序示例：

```python
a = [1,2,3]
print("a原地址为：{}".format(id(a)))
a += [4,5]
print("进行a += [4,5]操作后的a地址为：{}".format(id(a)))
a =a + [4,5]
print("进行a =a + [4,5]操作后的a地址为：{}".format(id(a)))

a = 1
print("a原地址为：{}".format(id(a)))
a += 2
print("进行a += 2操作后的a地址为：{}".format(id(a)))
a =a + 2
print("进行a =a + 2操作后的a地址为：{}".format(id(a)))
```

输出为：

```
a原地址为：140591562829000
进行a += [4,5]操作后的a地址为：140591562829000
进行a =a + [4,5]操作后的a地址为：140591562984456
a原地址为：4391820368
进行a += 2操作后的a地址为：4391820432
进行a =a + 2操作后的a地址为：4391820496
```



# 计算机网络相关



# 数据库相关



# Linux相关



# 数据结构与算法



# 其他

