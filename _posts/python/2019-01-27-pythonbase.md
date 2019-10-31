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
