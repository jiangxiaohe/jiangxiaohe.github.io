---
layout: post
title: python基础
tags:
- python
categories: python
description: python基础语法，重点在于其与C++和Java的区别
---

[TOC]

# 基本数据结构

## 字符串和格式化输入输出

python中字符串是常量，不可变

Python字符串格式化的两种方式：使用% 、 使用{}

* 使用%：
    %s     字符串
    %c     字符
    %d     十进制（整数）
    %i     整数
    %u    无符号整数
    %o     八进制整数
    %x    十六进制整数
    %X     十六进制整数大写
    %e     浮点数格式1
    %E     浮点数格式2
    %f     浮点数格式3
    %g    浮点数格式4
    %G    浮点数格式5
    %%     文字%
* 使用{}.foramt
    * "{} {}".format("hello", "world")
    * "{1} {0} {1}".format("hello", "world")
    * print("网站名：{name}, 地址 {url}".format(name="菜鸟教程", url="www.runoob.com"))
* 通过下标访问
    * person=['小李',20];
    * '我叫{0[0]}, 今年{0[1]}岁了'.format(person)
* 填充与对齐
    * 格式限定符，语法是{}中带:号.填充常跟对齐一起使用.
    * ^、<、>分别是居中、左对齐、右对齐，后面带宽度
    * :号后面带填充的字符，只能是一个字符，不指定的话默认是用空格填充
    ```
    >>> '{:>8}'.format('189')
    '     189'
    >>> '{:>8}'.format('189')
    '     189'
    >>> '{:0>8}'.format('189')
    '00000189'
    >>> '{:a>8}'.format('189')
    'aaaaa189'
    ```
* 精度与类型`m.nf`
* b、d、o、x分别是二进制、十进制、八进制、十六进制.
    * '{:b}'.format(17) '10001'

## bytes

python3以后，字符串和bytes彻底分开了。字符串是以字符为单位进行处理的，bytes是以字节为单位进行处理的。bytes数据类型在所有的操作和使用甚至内置方法上都和字符串类型是一样的，也是不可变的序列对象。

bytes对象只复杂以二进制字节序列的形式记录所需记录的对象，至于该对象到底表示什么，则由相应的编码格式所决定。

获取长度用`len()`

bytes转换为str：
`bs=str(b,encoding="utf8")`
`sb=bytes(s,encoding="utf8")`

## list

```python
#创建list
a=[]
b=[1,2]
c=['ab','cd']

len(list)#列表元素个数
max(list)#列表元素最大值
list(seq)#将元组转换为列表
```

## tuple元组

与list不同的是，tuple一般用()括起来

```python
T= 1,2,3
>>> T
(1,2,3)
#创建空元组
T=()
#只有一个元素的元组
T=(1)
```

## dict

创建空dict可以用a={}或a=dict()两种方式

```python
dict={'A':90,'b':85,'c':80}
for i in dict:
    print(i)
#打印出来的是key值
```

## set

* list转set `s = set(mylist)`
* 添加元素 `s.add(x)` 如果元素已存在，则不进行任何操作
* `s.update(x)` 这里，x可以是元素或列表，即可以同时添加多个元素
* 移除元素 `s.remove(x)` 如果元素不存在会发生错误
* 移除元素 `s.discard(x)` 如果元素不存在不会发生错误
* 计算集合元素个数 `len(s)`
* 清空集合 `s.clear()`
* 判断元素是否在集合中存在 `x in s`
* 返回两个集合的并集 `union(s1,s2)`


# 函数

```python
def quicksort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr) / 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quicksort(left) + middle + quicksort(right)
```

当内部函数有引用外部函数的同名变量或者全局变量,并且对这个变量有修改。
那么python会认为它是一个局部变量,又因为函数中没有定义和赋值，所以会报错。

解决方法是在函数内部对全局变量增加`global`关键字。

```python
j = 0
def f():
    global j
    print(j)
    j=j+1
```

## 内置函数

* `divmod(a, b)`函数把除数和余数运算结果结合起来，返回一个包含商和余数的元组(a // b, a % b)
* `abs()` 函数返回数字的绝对值
* `input()`Python3.x 中 input() 函数接受一个标准输入数据，返回为 string 类型。
* `open(name[, mode[, buffering]])`用于打开一个文件，创建一个 file 对象，相关的方法才可以调用它进行读写。
* `all()`用于判断给定的可迭代参数 iterable 中的所有元素是否都为 TRUE，如果是返回 True，否则返回 False。元素除了是 0、空、None、False 外都算 True。
* `any()`函数用于判断给定的可迭代参数 iterable 是否全部为 False，则返回 False，如果有一个为 True，则返回 True。
* `eval()`用来执行一个字符串表达式，并返回表达式的值。
* `ord() `是 chr() 函数（对于8位的ASCII字符串）或 unichr() 函数（对于Unicode对象）的配对函数，它以一个字符（长度为1的字符串）作为参数，返回对应的 ASCII 数值，或者 Unicode 数值，如果所给的 Unicode 字符超出了你的 Python 定义范围，则会引发一个 TypeError 的异常。
* `chr()` 用一个范围在 range（256）内的（就是0～255）整数作参数，返回一个对应的字符。
* `pow(x,y)` 返回（x的y次方） 的值。
* `sum(iterable[, start])`iterable -- 可迭代对象，如：列表、元组、集合.start -- 指定相加的参数，如果没有设置这个值，默认为0。
* `execfile(filename[, globals[, locals]])`可以用来执行一个文件。
* `issubclass(class, classinfo)` 方法用于判断参数 class 是否是类型参数 classinfo 的子类。
* `super()` 函数是用于调用父类(超类)的一个方法。
* `bin()` 返回一个整数 int 或者长整数 long int 的二进制表示。
* `round()` 方法返回浮点数x的四舍五入值。
* `file()` 函数用于创建一个 file 对象，它有一个别名叫 `open()`，更形象一些，它们是内置函数。参数是以字符串的形式传递的。
* `iter()` 函数用来生成迭代器。
* `tuple()` 函数将列表转换为元组
* `bool()` 函数用于将给定参数转换为布尔类型
* `len()` 方法返回对象（字符、列表、元组等）长度或项目个数。
* `range(start, stop[, step])` 函数可创建一个整数列表，一般用在 for 循环中。默认是从 0 开始。例如range（5）等价于range（0， 5）
* `callable(object)`函数用于检查一个对象是否是可调用的。如果返回 True，object 仍然可能调用失败；但如果返回 False，调用对象 object 绝对不会成功。对于函数、方法、lambda 函式、 类以及实现了 __call__ 方法的类实例, 它都返回 True。
* `locals()` 函数会以字典类型返回当前位置的全部局部变量。
* `class frozenset([iterable])`返回一个冻结的集合，冻结后集合不能再添加或删除任何元素。
* `reload()` 用于重新载入之前载入的模块。比如`reload(sys)`
* `vars()` 函数返回对象object的属性和属性值的字典对象。
* `getattr(object, name[, default])` 函数用于返回一个对象属性值.
* `hasattr(object, name)`用于判断对象是否包含对应的属性。
* `delattr(object, name)`函数用于删除属性。
* `setattr(object, name, value)`用于设置属性值，该属性不一定是存在的。
* `cmp(x,y)` 函数用于比较2个对象，如果 x < y 返回 -1, 如果 x == y 返回 0, 如果 x > y 返回 1。
* `reverse()` 函数用于反向列表中元素。调用`aList.reverse()`.`delattr(x, 'foobar')` 相等于 `del x.foobar`。
* `class complex([real[, imag]])`函数用于创建一个值为 real + imag * j 的复数或者转化一个字符串或数为复数。如果第一个参数为字符串，则不需要指定第二个参数。比如`complex("1+2j")`，`complex(1, 2)`
* `hex()` 函数用于将10进制整数转换成16进制，以字符串形式表示。
* `next()` 返回迭代器的下一个项目。
* `help()` 函数用于查看函数或模块用途的详细说明。





* `bytearray()` 方法返回一个新字节数组。这个数组里的元素是可变的，并且每个元素的值范围: 0 <= x < 256。
* `property()` 函数的作用是在新式类中返回属性值。

## `hash()`

用于获取取一个对象（字符串或者数值等）的哈希值。相同字符串在同一次运行时的哈希值是相同的，但是不同次运行的哈希值不同。这是由于Python的字符串hash算法有一个启动时随机生成secret prefix/suffix的机制，存在随机化现象：对同一个字符串输入，不同解释器进程得到的hash结果可能不同。因此当需要做可重现可跨进程保持一致性的hash，需要用到hashlib模块。

## `memoryview() `

返回给定参数的内存查看对象(Momory view)。

所谓内存查看对象，是指对支持缓冲区协议的数据进行包装，在不需要复制对象基础上允许Python代码访问。

```python
>>>v = memoryview(bytearray("abcefg", 'utf-8'))
>>> print(v[1])
98
>>> print(v[-1])
103
>>> print(v[1:4])
<memory at 0x10f543a08>
>>> print(v[1:4].tobytes())
b'bce'
```

## `getattr(object, name[, default])`

返回一个对象属性值

```python
class A(object):
...     bar = 1
... 
>>> a = A()
>>> getattr(a, 'bar')        # 获取属性 bar 值
1
>>> getattr(a, 'bar2')       # 属性 bar2 不存在，触发异常
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'A' object has no attribute 'bar2'
>>> getattr(a, 'bar2', 3)    # 属性 bar2 不存在，但设置了默认值
3
```

## `reduce(function, iterable[, initializer])`

function -- 函数，有两个参数.
iterable -- 可迭代对象.
initializer -- 可选，初始参数.

会对参数序列中元素进行累积。

函数将一个数据集合（链表，元组等）中的所有数据进行下列操作：用传给 reduce 中的函数 function（有两个参数）先对集合中的第 1、2 个元素进行操作，得到的结果再与第三个数据用 function 函数运算，最后得到一个结果。

```python
def add(x, y) :            # 两数相加
    return x + y
 
>>> reduce(add, [1,2,3,4,5])   # 计算列表和：1+2+3+4+5
15
>>> reduce(lambda x, y: x+y, [1,2,3,4,5])  # 使用 lambda 匿名函数
15
```

## `filter(function, iterable)`

用于过滤序列，过滤掉不符合条件的元素，返回由符合条件元素组成的新列表。

该接收两个参数，第一个为函数，第二个为序列，序列的每个元素作为参数传递给函数进行判断，然后返回 True 或 False，最后将返回 True 的元素放到新列表中。

```python
# 过滤出列表中的所有奇数
def is_odd(n):
    return n % 2 == 1
 
newlist = filter(is_odd, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
print(newlist)

# 过滤出1~100中平方根是整数的数
import math
def is_sqr(x):
    return math.sqrt(x) % 1 == 0
 
newlist = filter(is_sqr, range(1, 101))
print(newlist)
```


## `isinstance()`

isinstance() 函数来判断一个对象是否是一个已知的类型，类似 type()

isinstance() 与 type() 区别：
* type() 不会认为子类是一种父类类型，不考虑继承关系。
* isinstance() 会认为子类是一种父类类型，考虑继承关系。

如果要判断两个类型是否相同推荐使用 isinstance()。

```python
class A:
    pass
 
class B(A):
    pass
 
isinstance(A(), A)    # returns True
type(A()) == A        # returns True
isinstance(B(), A)    # returns True
type(B()) == A        # returns False
```

## `staticmethod()`

返回函数的静态方法，用@不需要写在函数前。

```python
class C(object):
    @staticmethod
    def f():
        print('runoob');
 
C.f();          # 静态方法无需实例化
cobj = C()
cobj.f()        # 也可以实例化后调用
```

## `classmethod` 

修饰符对应的函数不需要实例化，不需要 self 参数，但第一个参数需要是表示自身类的 cls 参数，可以来调用类的属性，类的方法，实例化对象等。

```python
class A(object):
    bar = 1
    def func1(self):  
        print ('foo') 
    @classmethod
    def func2(cls):
        print ('func2')
        print (cls.bar)
        cls().func1()   # 调用 foo 方法
 
A.func2()               # 不需要实例化
```

## map函数

map() 会根据提供的函数对指定序列做映射。第一个参数 function 以参数序列中的每一个元素调用 function 函数，返回包含每次 function 函数返回值的新列表。

`map(function, iterable, ...)`

```python
def square(x) :            # 计算平方数
    return x ** 2
map(square, [1,2,3,4,5]) # [1, 4, 9, 16, 25]
map(lambda x: x ** 2, [1, 2, 3, 4, 5]) # [1, 4, 9, 16, 25]

# 提供了两个列表，对相同位置的列表数据进行相加
map(lambda x, y: x + y, [1, 3, 5, 7, 9], [2, 4, 6, 8, 10])
# [3, 7, 11, 15, 19]
```

## `zip([iterable, ...])`函数

zip() 函数用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的对象，这样做的好处是节约了不少的内存。

我们可以使用 list() 转换来输出列表。

如果各个迭代器的元素个数不一致，则返回列表长度与最短的对象相同，利用 * 号操作符，可以将元组解压为列表。

```python
>>> a = [1,2,3]
>>> b = [4,5,6]
>>> c = [4,5,6,7,8]
>>> zipped = zip(a,b)     # 返回一个对象
>>> zipped
<zip object at 0x103abc288>
>>> list(zipped)  # list() 转换为列表
[(1, 4), (2, 5), (3, 6)]
>>> list(zip(a,c))              # 元素个数与最短的列表一致
[(1, 4), (2, 5), (3, 6)]

>>> a1, a2 = zip(*zip(a,b))          # 与 zip 相反，zip(*) 可理解为解压，返回tutle类型，而非ndarray
>>> list(a1)
[1, 2, 3]
>>> list(a2)
[4, 5, 6]
```

## `sorted(iterable[, cmp[, key[, reverse]]])`函数

* sorted() 函数对所有可迭代的对象进行排序操作。
* sort 是应用在 list 上的方法，sorted 可以对所有可迭代的对象进行排序操作。
* list 的 sort 方法返回的是对已经存在的列表进行操作，无返回值，而内建函数 sorted 方法返回的是一个新的 list，而不是在原来的基础上进行的操作。
* iterable -- 可迭代对象。
* cmp -- 比较的函数，这个具有两个参数，参数的值都是从可迭代对象中取出，此函数必须遵守的规则为，大于则返回1，小于则返回-1，等于则返回0。
* key -- 主要是用来进行比较的元素，只有一个参数，具体的函数的参数就是取自于可迭代对象中，指定可迭代对象中的一个元素来进行排序。
* reverse -- 排序规则，reverse = True 降序 ， reverse = False 升序（默认）。

```python
>>>a = [5,7,6,3,4,1,2]
>>> b = sorted(a)       # 保留原列表
>>> a
[5, 7, 6, 3, 4, 1, 2]
>>> b
[1, 2, 3, 4, 5, 6, 7]

>>> L=[('b',2),('a',1),('c',3),('d',4)]
>>> sorted(L, cmp=lambda x,y:cmp(x[1],y[1]))   # 利用cmp函数
[('a', 1), ('b', 2), ('c', 3), ('d', 4)]
>>> sorted(L, key=lambda x:x[1])               # 利用key
[('a', 1), ('b', 2), ('c', 3), ('d', 4)]


>>> students = [('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
>>> sorted(students, key=lambda s: s[2])            # 按年龄排序
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]

>>> sorted(students, key=lambda s: s[2], reverse=True)       # 按降序
[('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
```

## lambda 表达式:`lambda [arg1[, arg2, ... argN]]: expression`

* lambda只是一个表达式，函数体比def简单很多，很多时候定义def，然后写一个函数太麻烦，这时候就可以用lambda定义一个匿名函数。
* lambda可以定义一个匿名函数，而def定义的函数必须有一个名字。这应该是lambda与def两者最大的区别。
* 设计理念为：lambda是一个为编写简单的函数而设计的，而def用来处理更大的任务。
* lambda函数主要用来写一些小体量的一次性函数，避免污染环境，同时也能简化代码。
* lambda起到了一种函数速写的作用，允许在使用的代码内嵌入一个函数的定义。他们完全是可选的（你总是能够使用def来替代它们），但是你仅需要嵌入小段可执行代码的情况下它们会带来一个更简洁的代码结构。

## `enumerate(sequence, [start=0])`函数
* enumerate() 函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标，一般用在 for 循环当中。
* sequence -- 一个序列、迭代器或其他支持迭代对象。
* start -- 下标起始位置。
返回值
* 返回enumerate(枚举) 对象。

```python
>>> seasons = ['Spring', 'Summer', 'Fall', 'Winter']
>>> list(enumerate(seasons))
[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
>>> list(enumerate(seasons, start=1))       # 下标从 1 开始
[(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
# for循环中使用enumerate
>>> seq = ['one', 'two', 'three']
>>> for i, element in enumerate(seq):
...     print i, element
...
0 one
1 two
2 three
```


## 生成列表`for in`语句

```python
# 列出从1到10的平方
>>> [x*x for x in range(1,11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
# 添加判断条件
>>> [x*x for x in range(1,11) if x%2==0]
[4, 16, 36, 64, 100]
# 多个for同时判断
>>> [m+n for m in 'ABC' for n in'abc']
['Aa', 'Ab', 'Ac', 'Ba', 'Bb', 'Bc', 'Ca', 'Cb', 'Cc']
# 获取dict中的value
>>> d={'a': 'A', 'b': 'B', 'c': 'C'}
>>> [k + '=' + v for k,v in d.items()]
['c=C', 'a=A', 'b=B']
# 将list中所有的字符串变成小写
>>> L = ['Hello', 'World', 'IBM', 'Apple']
>>> [s.lower() for s in L]
['hello', 'world', 'ibm', 'apple']
```


# 类class

```python
class Greeter(object):

    # Constructor 构造函数在对象创建时自动运行
    def __init__(self, name):
        self.name = name  # Create an instance variable

    # Instance method
    # 对象内置对象调用时需要用self.对象名
    def greet(self, loud=False):
        if loud:
            print 'HELLO, %s!' % self.name.upper()
        else:
            print 'Hello, %s' % self.name

g = Greeter('Fred')  # Construct an instance of the Greeter class
g.greet()            # Call an instance method; prints "Hello, Fred"
g.greet(loud=True)   # Call an instance method; prints "HELLO, FRED!"
```

## 继承

```python
class Father(object):
    def __init__(self, name):
        self.name=name
        print ( "name: %s" %( self.name))
    def getName(self):
        return 'Father ' + self.name

class Son(Father):
    def __init__(self, name):
        super(Son, self).__init__(name)
        print ("hi")
        self.name =  name
    def getName(self):
        return 'Son '+self.name

if __name__=='__main__':
    son=Son('runoob')
    print ( son.getName() )

'''
name: runoob
hi
Son runoob
'''
```



# 其他

## [生成可执行文件](https://blog.csdn.net/woshisangsang/article/details/73230433)

使用工具pyinstaller `pip install pyinstaller`

使用`pyinstaller -V`查看是否安装成功

参数作用：
* -F 表示生成单个可执行文件
* -D –onedir 创建一个目录，包含exe文件，但会依赖很多文件（默认选项）
* -w 表示去掉控制台窗口，这在GUI界面时非常有用。不过如果是命令行程序的话那就把这个选项删除吧
* -c –console, –nowindowed 使用控制台，无界面(默认)
* -p 表示你自己自定义需要加载的类路径，一般情况下用不到
* -i 表示可执行文件的图标
* 其他参数，可以通过pyinstaller --help查看

示例：`pyinstaller.py -F -p C:\python27; -i ..\a.ico ..\demo.py`

当前目录下会多出一个dist目录，此目录下就是Python文件生成的exe可执行文件。

如果运行不成功，请安装pywin32，[pywin32网址](https://sourceforge.net/projects/pywin32/files/pywin32/)

双击pywin32-221.win-amd64-py3.6.exe安装

在CMD命令行进入Python3.6目录下的Scripts目录并执行：python pywin32_postinstall.py -install

在ubuntu系统上运行该命令，生成的是适合ubuntu系统的可执行文件，在win系统上运行该命令，则生成exe文件。

## 函数接受命令行参数

```
# 代码
import sys
print '参数个数为:', len(sys.argv), '个参数。'
print '参数列表:', str(sys.argv)

# 命令行运行如下：
$ python test.py arg1 arg2 arg3
参数个数为: 4 个参数。
参数列表: ['test.py', 'arg1', 'arg2', 'arg3']
```

## `if __name__`的用法

```python
if __name__ == '__main__':
    url='http://www.baidu.com'
    print(getHTMLText(url))
```
一个.py文件，如果是自身在运行，那么他的__name__值就是`"__main__"`

如果它是被别的程序导入的（作为一个模块），那么，他的__name__就不是`"__main__"`了。

所以，在.py文件中使用这个条件语句，可以使这个条件语句块中的命令只在它独立运行时才执行

## pip换源

可以在使用pip的时候加参数`-i https://pypi.tuna.tsinghua.edu.cn/simple`

例如：`pip install SomePackage -i https://pypi.tuna.tsinghua.edu.cn/simple`
这样就会从清华这边的镜像去安装SomePackage库。


## 函数运行时间time函数
单位秒

```python
import time
start=time.clock()
end=time.clock()
print("Time used: %d s"%(end-start))
```

## 时间格式替换

```python
from datetime import datetime
datetime.fromtimestamp(1571650483).strftime('%Y-%m-%d %H:%M:%S')
```
