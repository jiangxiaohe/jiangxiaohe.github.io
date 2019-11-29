---
layout: post
title: Kalman Filteer
tags:
categories: opencv
description: 经典卡尔曼滤波算法
---

# 卡尔曼滤波

可以在任何含有不确定信息的动态系统中使用卡尔曼滤波，对系统下一步的走向做出有根据的预测，即使伴随着各种干扰，卡尔曼滤波总是能指出真实发生的情况。

在连续变化的系统中使用卡尔曼滤波是非常理想的，它具有占用内存小的优点（除了前一个状态量外，不需要保留其它历史数据），并且速度很快，很适合应用于实时问题和嵌入式系统。

在[详解卡尔曼滤波原理](https://blog.csdn.net/u010720661/article/details/63253509)中，有详细的例子说明。

几个重要的概念：
* 状态变量服从高斯分布
* 状态变量
* 协方差矩阵
* 状态转移矩阵F：由状态转移矩阵F来更新状态向量和协方差矩阵
* 外部控制：控制矩阵、控制向量
* 外部干扰

$$
\begin{array}{l}{\hat{\mathbf{x}}_{k}=\mathbf{F}_{k} \hat{\mathbf{x}}_{k-1}+\mathbf{B}_{k} \overrightarrow{\mathrm{u}_{k}}} \\ {\mathbf{P}_{k}=\mathbf{F}_{\mathbf{k}} \mathbf{P}_{k-1} \mathbf{F}_{k}^{T}+\mathrm{Q}_{k}}\end{array}
$$

由上式可知，新的最优估计是根据上一最优估计预测得到的，并加上已知外部控制量的修正。而新的不确定性由上一不确定性预测得到，并加上外部环境的干扰。

用测量值来修正估计值：


