---
layout: post
title: python基础
tags:
- python
categories: python
description: python基础语法，重点在于其与C++和Java的区别
---

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
