---
layout: post
title: python爬虫入门2-beautifulsoup4
tags:
- 爬虫
categories: python
description:
---
# BeautifulSoup库的安装

BeautifulSoup库对应了html文档的全部内容，是解析、遍历、维护“标签树”的功能库

`<p class="title">hello</p>`
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

|元素|说明|
|-|-|
|Tag|标签，信息组织单元|
|Name|标签的名字，<p></p>的名字是'p'，格式<tag>.name|
|Attributes|标签的属性，字典形式组织，格式<tag>.attrs|
|NavigableString|标签内非属性字符串，`<p>……</p>`中的字符串，格式<tag>.string|
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

html是树形结构，有三种遍历方式：上行遍历、下行便利、平行遍历。

需要注意的是，字符串是和标签节点并列的类型。BeautifulSoup类型是标签树的根节点。

|遍历|属性|说明|
|-|-|-|
|下行|.contents|子节点的列表，将<tag>所有儿子节点存入列表|
|下行|.children|子节点的迭代类型，与.contents类似，用于循环遍历儿子节点|
|下行|.descendants|子孙节点的迭代类型，包含所有子孙节点，用于循环遍历|
|上行|.parent|节点的父亲标签|
|上行|.parents|节点先辈标签的迭代类型，用于循环遍历先辈节点|
|平行|.next_sibling|返回按照HTML文本顺序的下一个平行节点标签|
|平行|.previous_sibling|返回按照HTML文本顺序的上一个平行节点标签|
|平行|.next_siblings|迭代类型，返回按照HTML文本顺序的后续所有平行节点标签|
|平行|.previous_siblings|迭代类型，返回按照HTML文本顺序的前续所有平行节点标签|

```python
#b遍历儿子节点
for child in soup.body.children:
	print(child)
#遍历子孙节点
for child in soup.body.descendants:
	print(child)
#遍历父节点
for parent in soup.a.parents:
	if parent is None:
		print(parent)
	else
		print(parent.name)
#遍历平行节点
for sibling in soup.a.next_siblings:
	print(sibling)
for sibling in soup.a.previous_siblings:
	print(sibling)
```

# 基于bs4库的HTML格式化和编码

*更加友好的显示*：`prettify()`方法，为HTML文本<>及其内容增加更加`'\n'`。

例如：`soup.prettify()`或`soup.a.prettify()`

*bs4库的编码*：bs4库将任何HTML输入都变成utf‐8编码。
Python 3.x默认支持编码是utf‐8,解析无障碍。

# 信息标记的三种形式

## XML
eXtensible Markup Language

非空元素`<img src=“china.jpg” size=“10”> … </img>`

空元素`<img src=“china.jpg” size=“10” />`

注释`<!‐‐ This is a comment, very useful ‐‐>`

## JSON
JavsScript Object Notation，有类型的键值对key:value

```json
"name" : {
	"newName" : "北京理工大学",
	"oldName" : "延安自然科学院"
	}
```

这里，name是类型，{}里面的是键值对，多个键值对之间用逗号隔开

## YAML
YAML Ain’t Markup Language，无类型键值对key:value
* 缩进表达所属关系
* `-`表示并列关系
* `|`表达整块数据
* `#`表示注释

```YAML
name:
	newName : 北京理工大学
	oldName : 延安自然科学院
name:
-北京理工大学
-延安自然科学院
text: | #学校介绍
北京理工大学创立于1940年，前身是延安自然科学院，是中国共产党创办的第一所理工科大学。

key : value
key : #Comment
‐value1
‐value2
key :
	subkey : subvalue
```

三种标记形式对比：

|标记形式|比较|
|-|-|
|XML|最早的通用信息标记语言，可扩展性好，但繁琐|
|JSON|信息有类型，适合程序处理(js)，较XML简洁|
|YAML|信息无类型，文本信息比例最高，可读性好|

# 信息提取的一般方法

方法一：完整的解析信息的标记形式，再提取关键信息。例如bs4库的标签树遍历。

优点在于信息解析准确，缺点是过程繁琐，速度慢。

方法二：无视标签形式，直接搜索关键信息

例：提取HTML中所有的URL链接

```python
for link in soup.find_all('a'):
	print(link.get('href'))#这一句可以换做link['href'],因为link是字典类型
```

## BeautifulSoup的find_all方法

`soup.find_all(name, attrs, recursive, string, **kwargs)`

返回一个列表类型，存储查找的结果
* name:对标签名称的检索字符串
* attrs:对标签属性值的检索字符串，可标注属性检索
* recursive: 是否对子孙全部检索，默认True
* string: <>…</>中字符串区域的检索字符串

`<tag>(..)` 等价于`<tag>.find_all(..)`

`soup(..)` 等价于`soup.find_all(..)`

扩展方法


|方法|说明|
|-|-|
|<>.find()| 搜索且只返回一个结果，同.find_all()参数|
|<>.find_parents()| 在先辈节点中搜索，返回列表类型，同.find_all()参数|
|<>.find_parent()| 在先辈节点中返回一个结果，同.find()参数|
|<>.find_next_siblings()| 在后续平行节点中搜索，返回列表类型，同.find_all()参数|
|<>.find_next_sibling()| 在后续平行节点中返回一个结果，同.find()参数|
|<>.find_previous_siblings()| 在前序平行节点中搜索，返回列表类型，同.find_all()参数|
|<>.find_previous_sibling()| 在前序平行节点中返回一个结果，同.find()参数|


# 实例1：中国大学排名定向爬虫

* 爬取页面`http://www.zuihaodaxue.cn/zuihaodaxuepaiming2016.html`
* 检查爬虫可行性之robots协议`http://www.zuihaodaxue.cn/robots.txt`，此网站不存在此文件，所以爬虫可行
* 通过观察html代码，可以发现，所有大学的信息在一个tbody标签中，每个大学的信息在一个tr标签中，之下有若干个td标签，表示标签内的内容
* 代码中体现了用`chr(12288)`来解决中文字符对齐的问题

```python
# 实例1：中国大学排名定向爬虫
import requests
import bs4
from bs4 import BeautifulSoup
import re

def getHTMLText(url):
    try:
        r = requests.get(url)
        r.raise_for_status()  # 如果状态不是200，引发HTTPError异常
        r.encoding = r.apparent_encoding
        return r.text
    except:
        print('产生异常')
        return ''

def fillUnivList(html,list):
    soup=BeautifulSoup(html,'html.parser')
    for tr in soup.find('tbody').children:
        if isinstance(tr,bs4.element.Tag):
            tds=tr('td')
            list.append([tds[0].string,tds[1].string,tds[2].string])

def printUnivList(list,num):
    for rank in range(num):
        print("{0:5}{1:{3}<10}{2}".format(list[rank][0],list[rank][1],list[rank][2],chr(12288)))
    #这里chr(12288)是中文的空格

def main():
    url='http://www.zuihaodaxue.cn/zuihaodaxuepaiming2016.html'
    html=getHTMLText(url)
    list=[]
    fillUnivList(html,list)
    printUnivList(list,20)

main()

```
