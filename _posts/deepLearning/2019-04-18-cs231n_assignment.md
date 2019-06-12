---
layout: post
title: cs231n计算机视觉作业
tags:
categories: deepLearning
description: 课程笔记
---

# 作业1

本作业的目标如下：

* 理解基本的图像分类流程和数据驱动方法（训练与预测阶段）。
* 理解训练、验证、测试分块，学会使用验证数据来进行超参数调优。
* 熟悉使用numpy来编写向量化代码。
* 实现并应用k-最近邻（k-NN）分类器。
* 实现并应用Softmax分类器。
* 实现并应用支持向量机（SVM）分类器。
* 实现并应用一个两层神经网络分类器。
* 理解以上分类器的差异和权衡之处。
* 基本理解使用更高层次表达相较于使用原始图像像素对算法性能的提升（例如：色彩直方图和梯度直方图HOG）。


* Q1：k-最近邻分类器（20分）knn.ipynb
* Q2：训练一个SVM（25分）svm.ipynb
* Q3：实现Softmax分类器（20分）softmax.ipynb
* Q4：实现2层神经网络（25分）two_layer_net.ipynb
* Q5：更高层次表达：图像特征（10分）features.ipynb

# 作业2

本作业的目标如下：

* 理解神经网络及其分层结构。
* 理解并实现（向量化）反向传播。
* 实现多个用于神经网络最优化的更新方法。
* 实现用于训练深度网络的批量归一化（ batch normalization ）。
* 实现随机失活（dropout）。
* 进行高效的交叉验证并为神经网络结构找到最好的超参数。
* 理解卷积神经网络的结构，并积累在数据集上训练此类模型的经验。


* Q1：全连接神经网络（30分）FullyConnectedNets.ipynb
* Q2：批量归一化（30分）BatchNormalization.ipynb
* Q3：随机失活（Dropout）（10分）Dropout.ipynb
* Q4：在CIFAR-10上运行卷积神经网络（30分）ConvolutionalNetworks.ipynb


# 作业3

在本作业中，你将实现循环网络，并将其应用于在微软的COCO数据库上进行图像标注。我们还会介绍TinyImageNet数据集，然后在这个数据集使用一个预训练的模型来查看图像梯度的不同应用。本作业的目标如下：

* 理解循环神经网络（RNN）的结构，知道它们是如何随时间共享权重来对序列进行操作的。
* 理解普通循环神经网络和长短基记忆（Long-Short Term Memory）循环神经网络之间的差异。
* 理解在测试时如何从RNN生成序列。
* 理解如何将卷积神经网络和循环神经网络结合在一起来实现图像标注。
* 理解一个训练过的卷积神经网络是如何用来从输入图像中计算梯度的。
* 进行高效的交叉验证并为神经网络结构找到最好的超参数。
* 实现图像梯度的不同应用，比如显著图，搞笑图像，类别可视化，特征反演和DeepDream。

* Q1：使用普通RNN进行图像标注（40分）RNN_Captioning.ipynb
* Q2：使用LSTM进行图像标注（35分）LSTM_Captioning.ipynb
* Q3：图像梯度：显著图和高效图像（10分）ImageGradients.ipynb
* Q4：图像生成：类别，反演和DeepDream（30分）ImageGeneration.ipynb




0
