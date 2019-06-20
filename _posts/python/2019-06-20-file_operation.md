---
layout: post
title: python文件操作
tags:
- python
categories: python
description: python基础语法，重点在于其与C++和Java的区别
---

# OS库


* 返回文件夹包含的文件或文件夹的名字的列表
`os.listdir(path)`
* 判断文件或者文件夹是否存在
`os.path.exists(path)`
* 删除文件
  * 删除单个文件`os.remove(path)`
  * 删除空文件夹`os.rmdir(path)`
  * 递归删除文件和文件夹`import shutil` `shutil.rmtree()`
  * shutil模块提供了许多关于文件和文件集合的高级操作，特别提供了支持文件复制和删除的功能。

遍历文件代码示例：

```python
import os
for root, dirs, files in os.walk(top, topdown=False):
    for name in files:
        os.remove(os.path.join(root, name))
    for name in dirs:
        os.rmdir(os.path.join(root, name))
```
