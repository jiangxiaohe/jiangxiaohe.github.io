---
layout: post
title: 视音频编解码技术
tags:
categories: computerScience
description:
---

[TOC]

[雷神CSDN](https://blog.csdn.net/leixiaohua1020/article/details/18893769)


不同的视频后缀avi，rmvb，mp4，flv，mkv等代表的是封装格式。何为封装格式？就是把视频数据和音频数据打包成一个文件的规范。仅仅靠看文件的后缀，很难能看出具体使用了什么视音频编码标准。总的来说，不同的封装格式之间差距不大，各有优劣。

注：有些封装格式支持的视音频编码标准十分广泛，应该算比较优秀的封装格式，比如MKV；而有些封装格式支持的视音频编码标准很少，应该属于落后的封装格式，比如RMVB。

# 1 视频播放器原理
