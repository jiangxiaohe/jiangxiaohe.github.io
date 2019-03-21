---
layout: post
title: matplotlib常用函数
tags:
- python
categories: python
description: 学习笔记
---
`import matplotlib.plot as plt`

## .plot(x,y)
`plt.plot(x,y,format_string,**kwargs)`

x为x轴数据，可为列表或数组；y同理；format_string 为控制曲线的格式字符串，kwargs 第二组或更多的（x, y, format_string）

format_string: 由 颜色字符、风格字符和标记字符组成。

颜色字符：‘b’蓝色  ；‘#008000’RGB某颜色；‘0.8’灰度值字符串

风格字符：‘-’实线；‘--’破折线； ‘-.’点划线； ‘：’虚线 ； ‘’‘’无线条

标记字符：‘.’点标记  ‘o’ 实心圈 ‘v’倒三角  ‘^’上三角

## 坐标和表头
pyplot并不默认支持中文显示，需要rcParams修改字体来实现

rcParams的属性：

‘font.family’ 用于显示字体的名字

‘font.style’ 字体风格，正常’normal’ 或斜体’italic’

‘font.size’ 字体大小，整数字号或者’large’   ‘x-small’

```python
import matplotlib
matplotlib.rcParams[‘font.family’] = ‘STSong’
matplotlib.rcParams[‘font.size’] = 20
```
设定绘制区域的全部字体变成 华文仿宋，字体大小为20

中文显示2：只希望在某地方绘制中文字符，不改变别的地方的字体

在有中文输出的地方，增加一个属性： fontproperties

`plt.xlabel(‘横轴：时间’, fontproperties = ‘simHei’, fontsize = 20)`
pyplot文本显示函数：

plt.xlabel()：对x轴增加文本标签

plt.ylabel()：同理

plt.title(): 对图形整体增加文本标签

plt.text(): 在任意位置增加文本

## .subplot()
plt.subplot(3,2,4) :  分成3行2列，共6个绘图区域，在第4个区域绘图。排序为行优先。也可 plt.subplot(324)，将逗号省略。

## .show()
plot方法之后用show方法显示出来

## .imshow()

```python
img = cv2.imread('messi5.jpg',0)
plt.imshow(img, cmap = 'gray', interpolation = 'bicubic')
plt.xticks([]), plt.yticks([])  # to hide tick values on X and Y axis
plt.show()
```
