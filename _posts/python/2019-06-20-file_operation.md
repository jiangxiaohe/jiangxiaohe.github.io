---
layout: post
title: python文件操作
tags:
- python
categories: python
description: python基础语法，重点在于其与C++和Java的区别
---

[TOC]


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
