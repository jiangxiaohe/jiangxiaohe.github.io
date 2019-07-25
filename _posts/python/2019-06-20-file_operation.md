---
layout: post
title: python文件操作
tags:
- python
categories: python
description: python基础语法，重点在于其与C++和Java的区别
---

[TOC]

# open函数
python open() 函数用于打开一个文件，创建一个 `file 对象`，相关的方法才可以调用它进行读写。

`open(name[, mode[, buffering]])`

常见的模式如下：
* r 以只读方式打开文件。文件的指针将会放在文件的开头。这是默认模式。
* r+	打开一个文件用于读写。文件指针将会放在文件的开头。
* w	打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。
* w+	打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。
* a	打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。
* a+	打开一个文件用于读写。如果该文件已存在，文件指针将会放在文件的结尾。文件打开时会是追加模式。如果该文件不存在，创建新文件用于读写。

## with open写法

文件使用完毕后必须关闭，因为文件对象会占用操作系统的资源，并且操作系统同一时间能打开的文件数量也是有限的。

由于文件读写时都有可能产生IOError，一旦出错，后面的f.close()就不会调用。所以，为了保证无论是否出错都能正确地关闭文件，我们可以使用try ... finally来实现：

```python
try:
    f = open('/path/to/file', 'r')
    print(f.read())
finally:
    if f:
        f.close()
```

但是每次都这么写实在太繁琐，所以，Python引入了with语句来自动帮我们调用close()方法：

```python
with open(file_path, 'r') as f:
    print(f.read())
```

# OS

* 返回文件夹包含的文件或文件夹的名字的列表
`os.listdir(path)`
* 判断文件或者文件夹是否存在
`os.path.exists(path)`
* 删除文件
  * 删除单个文件`os.remove(path)`
  * 删除空文件夹`os.rmdir(path)`

遍历文件代码示例：

```python
import os
import shutil
src = 'video3'
dst = 'video_test_10g/video'
num = -1
f = open(file,'a')
for root, dirs, files in os.walk(src, topdown=False):
    print(len(files))
    for name in files[:num]:
        print(name)
        f.write(name)
        f.write('\r\n')#只有这样才能换行
f.close()
```

很多时候为了方便操作，会将所有文件夹内的文件名存为txt文件，然后直接读取txt文件，这样会获得比os.walk更快的读取速度。更重要的是，获得更便捷的后续操作，比如断点续传。

# shutil
shutil模块提供了许多关于文件和文件集合的高级操作，特别提供了支持文件复制和删除的功能。

* 递归删除文件和文件夹`import shutil` `shutil.rmtree()`
* 复制文件`shutil.copyfile( src, dst)`
* 移动文件或重命名`shutil.move( src, dst)`

```
def del_dir(path):
    if os.path.exists(path):
        shutil.rmtree(path)  
    os.mkdir(path)
```

# csv
python自带了csv模块提供用户对csv文件进行读写操作。
```python
with open(csv_path)as f:
    f_csv = csv.reader(f)
    for row in f_csv:
        print(row) # 这里row的类型是list，要读取各个数据，请用row[rank]


# a+表示追加，不存在则创建
def write_lsit_to_csv(csv_path,line_list):
    with open(csv_path,'a') as f:
        csv_write = csv.writer(f)
        csv_write.writerow(line_list)

headers = ['class','name','sex','height','year']        
rows = [
        {'class':1,'name':'xiaoming','sex':'male','height':168,'year':23},
        {'class':1,'name':'xiaohong','sex':'female','height':162,'year':22},
        {'class':2,'name':'xiaozhang','sex':'female','height':163,'year':21},
        {'class':2,'name':'xiaoli','sex':'male','height':158,'year':21},
    ]
# 注意体会两者的不同
def write_rows_to_csv(csv_path,rows_dict,headers)        
    with open(csv_path,'a') as f:
        f_csv = csv.DictWriter(f,headers)
        f_csv.writeheader()
        f_csv.writerows(rows_dict)
def write_row_to_csv(csv_path,row_dict)        
    with open(csv_path,'a') as f:
        f_csv.writerow(row_dict)
```

# glob

glob是python自己带的一个文件操作相关模块，用它可以查找符合自己目的的文件，就类似于Windows下的文件搜索，支持通配符操作，`*,?,[]`这三个通配符，`*`代表0个或多个字符，`?`代表一个字符，`[]`匹配指定范围内的字符，如[0-9]匹配数字。

正则表达式匹配示例：
* `glob.glob('dir/*/*')`
* `glob.glob('dir/file?.txt')`
* `glob.glob('dir/*[0-9].*')`

它的主要方法就是glob,该方法返回所有匹配的文件路径列表，该方法需要一个参数用来指定匹配的路径字符串（本字符串可以为绝对路径也可以为相对路径），**其返回的文件名只包括当前目录里的文件名，不包括子文件夹里的文件**。

`glob.glob(r'.csv')`返回当前目录中所有的csv文件，返回是一个列表，是所有的csv文件的路径，如果使用的是相对路径，返回的也是相对路径，如果使用的是绝对路径，那么返回的也是绝对路径。



# pandas

我们越来越多的使用pandas进行数据处理，其效率较高。

```python
def write_list_to_csv(csv_path,index_list):
    test=pd.DataFrame(columns=None,data=index_list)
    test.to_csv(csv_path,mode='a+', header=False,index=False)

def read_csv_to_list(csv_path):
    return pd.read_csv(csv_path,header = None).values.tolist()
```
