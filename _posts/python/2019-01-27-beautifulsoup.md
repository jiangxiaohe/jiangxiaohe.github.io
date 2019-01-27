---
layout: post
title: python爬虫入门2-beautifulsoup4
tags:
- python
categories: python
description:
---
# BeautifulSoup库的安装

BeautifulSoup库对应了html文档的全部内容，是解析、遍历、维护“标签树”的功能库

<p class="title">hello</p>

对于每一个标签tag，都有一个名称name和若干个属性attributes（属性以键值对的形式存在，比如，`class="title"`即是一个键值对）

安装`pip install beautifulsoup4`

使用

```python
from bs4 import BeautifulSoup
soup=BeautifulSoup(html_text,'html.parser')
```

第一个参数是html内容，第二个参数是解释器，常用的解释器如下：

|解析器|使用方法|条件|
|-|-|-|
|bs4的HTML解析器|BeautifulSoup(mk,'html.parser')|安装bs4库|
|lxml的HTML解析器|BeautifulSoup(mk,'lxml')|pip install lxml|
|lxml的XML解析器|BeautifulSoup(mk,'xml')|pip install lxml|
|html5lib的解析器|BeautifulSoup(mk,'html5lib')|pip install html5lib|

# Beautiful Soup类的基本元素

|Tag|标签，最近本的信息组织单元|
|Name|标签的名字，<p></p>的名字是'p'，格式<tag>.name|
|Attributes|标签的属性，字典形式组织，格式<tag>.attrs|
|NavigableString|标签内非属性字符串，<p>……</p>中的字符串，格式<tag>.string|
|Comment|标签内字符串的注释部分，一种特殊的Comment类型|

```python
url='https://python123.io/ws/demo.html'
soup.title #可获取页面的title标签中的信息
soup.a #获取页面所有的a标签中的第一个
soup.a.name #获取a标签的名字，当然是'a'了
soup.a.parent.name #获取a标签的父标签
soup.a.attrs #a标签中所有的属性键值对，以字典形式存储
soup.a.attrs['class'] #获取key=class时的值
type(soup.a.attrs) #返回结果是<class 'dict'>，表示返回值是字典类型
soup.a.string
```

# 基于bs4库的HTML内容遍历方法
# 基于bs4库的HTML格式化和编码
