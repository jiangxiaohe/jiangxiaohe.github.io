---
layout: post
title: tensorBoardX
tags:
categories: deepLearning
description:
---

* [官方tutorial](https://tensorboardx.readthedocs.io/en/latest/tutorial.html)
* [demo](https://github.com/lanpa/tensorboardX/blob/master/examples/demo.py)

```
# begin
from tensorboardX import SummaryWriter
writer = SummaryWriter()

# do something

# end
# export scalar data to JSON for external processing
writer.export_scalars_to_json("./all_scalars.json")
writer.close()
```

# 添加标量scalar

保存每个训练步骤逇损失值和准确率、召回率。如果是pytorch张量，用tensor.item()即可

* 添加单变量`writer.add_scalar('myscalar',value, n_iter)`
第一个参数可以简单理解为保存图的名称，第二个参数是可以理解为Y轴数据，第三个参数可以理解为X轴数据。当Y轴数据不止一个时，可以使用writer.add_scalars()

# 添加图片image

`writer.add_image('imresult', x, iteration)`
* 其中，x.shape=[batch_size,c,h,w]
* x为3维张量
* 这里的图像需要标准化，即数值归一化到[0,1)

# 添加直方图histogram
保存直方图非常耗费资源。如果使用此软件包后训练速度变慢，请首先检查此内容。要保存直方图，请将数组转换为numpy数组，然后使用保存。

`writer.add_histogram('hist', array, iteration)`

* 记录网络参数变化，这里name是str类型，param的类型是torch.nn.parameter.Parameter，即为tensor类型
```
for name, param in model.named_parameters():
    writer.add_histogram(name, param, n_iter)
```

# 添加嵌入embedding
嵌入，高维数据可以通过张量板可视化并转换为人类可感知的3D数据，从而提供PCA和t-sne将数据投影到低维空间中。您需要做的就是提供很多点，张量板将为您完成其余工作。

# 添加pr curve
`add_pr_curve (tag, labels, predictions, step)`
labels是标准答案，predictions是程式对样本的预测。

# 添加图表
pyplot的图表想要记录，需要用add_figure.
