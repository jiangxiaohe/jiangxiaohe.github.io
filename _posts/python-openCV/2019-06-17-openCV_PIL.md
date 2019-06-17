---
layout: post
title: PIL库和opencv
tags:
- openCV
categories: python-openCV
description:
---
[图像处理库PIL与OpenCV](https://blog.csdn.net/LYKXHTP/article/details/81837951)

多数图像处理与操作技术可以被两个库有效完成，它们是Python 图形库 (Python Imaging Library，PIL) 与 开源计算机视觉 (OpenSource Computer Vision，OpenCV)。

Python Imaging Library ，或者叫PIL，简略来说， 是Python图像操作的核心库。不幸的是，它的开发陷入了停滞，最后一次更新是2009年。幸运的是，存在一个活跃的PIL开发分支，叫做 Pillow 它很容易安装，运行在各个操作系统上，而且支持Python3。

OpenSource Computer Vision,其更广为人知的名字是OpenCv，是一个在图像操作与处理上比PIL更先进的库。它可以在很多语言上被执行并被广泛使用。在Python中，使用OpenCV进行图像处理是通过使用 cv2 与 NumPy 模块进行的。

补充：PIL中所涉及的基本概念：
通道（bands）、模式（mode）、尺寸（size）、坐标系统（coordinate system）、调色板（palette）、信息（info）和滤波器（filters）。
