---
layout: post
title: 人脸识别
tags:
categories: deepLearning
description: 实验笔记
---

* [人脸识别入门](https://zhuanlan.zhihu.com/p/24567586)

人脸识别分为几个步骤：
1. 人脸检测，检测关键点和框
2. 针对脸部的不同姿势，进行脸部对齐
3. 给脸部编码

# 数据集

## LFW

无约束自然场景人脸识别数据集，该数据集由13000多张全世界知名人士互联网自然场景不同朝向、表情和光照环境人脸图片组成，共有5000多人，其中有1680人有2张或2张以上人脸图片。每张人脸图片都有其唯一的姓名ID和序号加以区分。
