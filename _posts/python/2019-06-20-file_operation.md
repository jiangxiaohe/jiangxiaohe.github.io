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
* 复制文件`shutil.copyfile( src, dst)`
* 移动文件或重命名`shutil.move( src, dst)`

遍历文件代码示例：

```python
import os
import shutil
src = 'video3'
dst = 'video_test_10g/video'
num = -1
for root, dirs, files in os.walk(src, topdown=False):
    print(len(files))
    for name in files[:num]:
        # print(src+'/'+name)
        # print(dst+'/'+name)
        # shutil.move(src+'/'+name,dst+'/'+name) # 移动文件
        # shutil.copyfile(src+'/'+name,dst+'/'+name) # 复制文件
        break
```

# 读写txt文件

# 利用pandas读写csv文件
我们越来越多的使用pandas进行数据处理，其效率较高。

```python

# 将列表(list)的数据写到csv 里面
import pandas as pd
file_path = 'file_path'
image_id = [397133, 37777, 252219, 87038]
name=['imageid']
test=pd.DataFrame(columns=name,data=image_id)
test.to_csv(file_path+'/'+'test.csv',index=False)

# 读取csv文件里面的数据并写到列表（list）里面
import pandas as pd
data = pd.read_csv(file_name)
list = data.values.tolist()

# 往csv文件中添加文件，默认读取模式为w，我们加上mode=1，便可以追加数据
df.to_csv('my_csv.csv', mode='a', header=False)
```
