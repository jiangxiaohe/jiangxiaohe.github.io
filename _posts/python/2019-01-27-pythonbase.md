---
layout: post
title: python基础
tags:
- python
categories: python
description: python基础语法，重点在于其与C++和Java的区别
---
# IO

* "{} {}".format("hello", "world")
* "{1} {0} {1}".format("hello", "world")
* print("网站名：{name}, 地址 {url}".format(name="菜鸟教程", url="www.runoob.com"))

# 数据类型

* bytes

python3以后，字符串和bytes彻底分开了。字符串是以字符为单位进行处理的，bytes是以字节为单位进行处理的。bytes数据类型在所有的操作和使用甚至内置方法上都和字符串类型是一样的，也是不可变的序列对象。

bytes对象只复杂以二进制字节序列的形式记录所需记录的对象，至于该对象到底表示什么，则由相应的编码格式所决定。

获取长度用`len()`

bytes转换为str：
`bs=str(b,encoding="utf8")`
`sb=bytes(s,encoding="utf8")`

* dict

```python
dict={'A':90,'b':85,'c':80}
for i in dict:
    print(i)
#打印出来的是key值
```

# 函数

```python
def add(x,y,f):
    return f(x)+f(y)
```

# 其他

* `if __name__`的用法

```python
if __name__ == '__main__':
    url='http://www.baidu.com'
    print(getHTMLText(url))
```
一个.py文件，如果是自身在运行，那么他的__name__值就是`"__main__"`

如果它是被别的程序导入的（作为一个模块），那么，他的__name__就不是`"__main__"`了。

所以，在.py文件中使用这个条件语句，可以使这个条件语句块中的命令只在它独立运行时才执行

# pip换源

可以在使用pip的时候加参数`-i https://pypi.tuna.tsinghua.edu.cn/simple`

例如：`pip install SomePackage -i https://pypi.tuna.tsinghua.edu.cn/simple`
这样就会从清华这边的镜像去安装SomePackage库。
