---
layout: post
title: TensorFlow搭建神经网络
tags:
categories: deepLearning
description:
---

[莫烦python教程](https://morvanzhou.github.io/tutorials/machine-learning/tensorflow/)
# mnist数据集测试

```python
import tensorflow as tf
from tensorflow.examples.tutorials.mnist import input_data
mnist = input_data.read_data_sets('MNIST_data',one_hot=True)

def add_layer(inputs,in_size,out_size,activation_function=None):
    Weights = tf.Variable(tf.random_normal([in_size,out_size]))
    biases = tf.Variable(tf.zeros([1,out_size])+0.1,)
    Wx_plus_b = tf.matmul(inputs,Weights)+biases
    if activation_function is None:
        outputs = Wx_plus_b
    else:
        outputs = activation_function(Wx_plus_b,)
    return outputs

def compute_accuracy(v_xs, v_ys):
    global prediction
    y_pre = sess.run(prediction, feed_dict={xs: v_xs})
    correct_prediction = tf.equal(tf.argmax(y_pre,1), tf.argmax(v_ys,1))
    accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
    result = sess.run(accuracy, feed_dict={xs: v_xs, ys: v_ys})
    return result

# define placeholder for inputs to network
xs = tf.placeholder(tf.float32, [None, 784]) # 28x28
ys = tf.placeholder(tf.float32, [None, 10])

# add output layer
prediction = add_layer(xs, 784, 10,  activation_function=tf.nn.softmax)

# the error between prediction and real data
cross_entropy = tf.reduce_mean(-tf.reduce_sum(ys * tf.log(prediction),reduction_indices=[1]))       # loss
train_step = tf.train.GradientDescentOptimizer(0.5).minimize(cross_entropy)

sess = tf.Session()
# important step
# tf.initialize_all_variables() no long valid from
init = tf.global_variables_initializer()
sess.run(init)

for i in range(1000):
    batch_xs, batch_ys = mnist.train.next_batch(100)
    sess.run(train_step, feed_dict={xs: batch_xs, ys: batch_ys})
    if i % 50 == 0:
        print(compute_accuracy(mnist.test.images, mnist.test.labels))
```


# tensorflow基础框架
* 安装anaconda
* `conda install tensorflow`

## 处理结构

Tensorflow 首先要定义神经网络的结构, 然后再把数据放入结构当中去运算和 training.

![tensorflow](https://www.tensorflow.org/images/tensors_flowing.gif)

tensorflow采用数据流图来计算，所以我们先创建一个数据流图，然后把数据（以张量(tensor)形式存在（放在数据流图中计算。节点在图中表示为数学操作，图中的边(edges)则表示在节点间相互联系的多维数据数组，即张量。训练模型时tensor会不断地从数据流图中的一个节点flow到另一节点。

标量是零阶张量，向量是一阶张量，矩阵是二阶张量。

## 一次直线拟合实例

```python
import tensorflow as tf
import numpy as np

# create data
x_data = np.random.rand(100).astype(np.float32)#tf中大部分数据是float32
y_data = x_data*0.1 + 0.3

# create tensorflow struct start ###

Weights = tf.Variable(tf.random_uniform([1],-1.0,1.0))#初始值是-1到1的随机数
biases = tf.Variable(tf.zeros([1]))#初始值为0

y = Weights * x_data + biases

loss = tf.reduce_mean(tf.square(y-y_data))

# 反向传递误差的工作就教给optimizer了, 我们使用的误差传递方法是梯度下降法: Gradient Descent 让后我们使用 optimizer 来进行参数的更新
optimizer = tf.train.GradientDescentOptimizer(0.5)
train = optimizer.minimize(loss)
# 以上是建立了神经网络的结构，还需要初始化所有之前定义的参数
init = tf.global_variables_initializer()

# create tensorflow struct end ###

# 创建会话session，用session来执行init初始化步骤，并且用session来run每一次training的数据，逐步提升神经网络的预测准确性
sess  = tf.Session()
sess.run(init)

for step in range(201):
    sess.run(train)
    if step%20 == 0:
        print(step,sess.run(Weights),sess.run(biases))
```

## Session会话控制

Session是tensorflow为了控制和输出文件的执行的语句，运行session.run()可以获得你要得知的运算结果，或者是你所要运算的部分。

例1：建立两个矩阵，输出两个矩阵相乘的结果

```python
import tensorflow as tf

matrix1 = tf.constant([[3,3]])
matrix2 = tf.constant([[2],[2]])

product = tf.matmul(matrix1,matrix2)

'''
# method 1
sess = tf.Session()
result = sess.run(product)
print(result)
sess.close()
'''

# method 2
with tf.Session() as sess:
    result2 = sess.run(product)
    print(result2)
```

代码中的两种方式都可以控制Session，采取其中一种即可。

## variable变量

定义变量`state = tf.Variable()`，注意，这里定义了某字符串是变量，它才是变量，这点与python不同。

```python
import tensorflow as tf

state = tf.Variable(0,name='counter')
# 定义常量
one = tf.constant(1)
# 定义加法步骤（注：此步并没有直接计算）
new_value = tf.add(state,one)
# 将state更新成 new_value
update = tf.assign(state,new_value)

# 注意：以上只是定义了变量但没有初始化，加上下面这句才行
init = tf.global_variables_initializer()

# 使用Session
with tf.Session() as sess:
    sess.run(init)
    for _ in range(4):
        sess.run(update)
        print(sess.run(state))
```

## Placeholder 传入值

placeholder 是 Tensorflow 中的占位符，暂时储存变量.Tensorflow 如果想要从外部传入data, 那就需要用到 tf.placeholder(), 然后以这种形式传输数据 `sess.run(***, feed_dict={input: **})`.

```python
import tensorflow as tf

#在 Tensorflow 中需要定义 placeholder 的 type ，一般为 float32 形式
input1 = tf.placeholder(tf.float32)
input2 = tf.placeholder(tf.float32)

# mul = multiply 是将input1和input2 做乘法运算，并输出为 output
ouput = tf.multiply(input1, input2)

# 传值的工作交给了sess.run()，需要传入的值放在了feed_dict={}并一一对应每一个 input. placeholder 与 feed_dict={} 是绑定在一起出现的。
with tf.Session() as sess:
    print(sess.run(ouput, feed_dict={input1: [7.], input2: [2.]}))
```

## 激励函数 (Activation Function)

激励函数的作用就是把线性函数变弯成非线性函数，其本质是一个非线性函数，比如`relu,sigmoid,tanh`，激励函数使得原有的线性结果发生了扭曲，使得输出结果y有了非线性的特征。

你甚至可以创造自己的激励函数来处理自己的问题, 不过要确保的是这些激励函数必须是可以微分的, 因为在 backpropagation 误差反向传递的时候, 只有这些可微分的激励函数才能把误差传递回去.

想要恰当使用这些激励函数, 还是有窍门的. 比如当你的神经网络层只有两三层, 不是很多的时候, 对于隐藏层, 使用任意的激励函数, 随便掰弯是可以的, 不会有特别大的影响. 不过, 当你使用特别多层的神经网络, 在掰弯的时候, 玩玩不得随意选择利器. 因为这会涉及到梯度爆炸, 梯度消失的问题.

在具体的例子中, 我们默认首选的激励函数是哪些. 在少量层结构中, 我们可以尝试很多种不同的激励函数. 在卷积神经网络 Convolutional neural networks 的卷积层中, 推荐的激励函数是 relu. 在循环神经网络中 recurrent neural networks, 推荐的是 tanh 或者是 relu。

# 搭建第一个神经网络

```python
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt
# 定义神经网络的一层
def add_layer(inputs,in_size,out_size,activation_function=None):
    Weights = tf.Variable(tf.random_normal([in_size,out_size]))
    biases = tf.Variable(tf.zeros([1,out_size]) + 0.1)#初始值不推荐为0
    Wx_plus_b = tf.matmul(inputs,Weights) + biases
    if activation_function is None:
        outputs = Wx_plus_b
    else:
        outputs = activation_function(Wx_plus_b)
    return outputs

# 导入数据
x_data = np.linspace(-1,1,300,dtype=np.float32)[:,np.newaxis]
noise = np.random.normal(0,0.05,x_data.shape)
y_data = np.square(x_data) - 0.5 + noise

# 利用占位符定义我们所需的神经网络的输入，tf.placeholder()代表占位符
# 这里的None代表无论输入有多少都可以，因为输入只有一个特征，所以这里是1。
xs = tf.placeholder(tf.float32,[None,1])
ys = tf.placeholder(tf.float32,[None,1])

# 搭建神经网络
lay1 = add_layer(xs,1,10,activation_function=tf.nn.relu)
prediction = add_layer(lay1,10,1,activation_function=None)
# 计算预测值prediction和真实值的误差，对二者差的平方求和再取平均
loss = tf.reduce_mean(tf.reduce_sum(tf.square(ys - prediction),
                     reduction_indices=[1]))
# 如何让机器学习提升它的准确率。tf.train.GradientDescentOptimizer()中的值通常都小于1，这里取的是0.1，代表以0.1的效率来最小化误差loss。
train_step = tf.train.GradientDescentOptimizer(0.1).minimize(loss)
# 使用变量之前先初始化
init = tf.global_variables_initializer()
# 定义session
sess = tf.Session()
# 只有session.run()才会执行我们定义的运算
sess.run(init)

# 可视化
fig = plt.figure()
ax = fig.add_subplot(1,1,1)
ax.scatter(x_data,y_data)
plt.ion()#使程序plot之后不暂停，是显示动图的关键
plt.show()

# 训练1000次
for i in range(1000):
    sess.run(train_step,feed_dict={xs:x_data,ys:y_data})
    # 每50步输出一下机器学习的误差
    if i % 50 == 0:
        # print(sess.run(loss,feed_dict={xs:x_data,ys:y_data}))
        try:
            ax.lines.remove(lines[0])
        except Exception:
            pass
        prediction_value = sess.run(prediction,feed_dict={xs:x_data})
        lines = ax.plot(x_data,prediction_value,'r-',lw=5)
        plt.pause(0.1)
plt.show()  
```

## 加速神经网络训练(Speed Up Training)

包括以下几种模式（以后再补充）
* Stochastic Gradient Descent (SGD)随即梯度下降
* Momentum
* AdaGrad
* RMSProp
* Adam

## 优化器 optimizer
Tensorflow 中的优化器会有7种。最基本, 也是最常用的一种就是GradientDescentOptimizer。
（以后补充）

# Tensorboard 可视化工具

用图像工具展示tensorflow整个神经网络的框架。

## 可视化神经网路的结构

关键语句`with tf.name_scope()`和定义变量时设置`name=''`参数

需要修改的代码有：

```python
def add_layer(inputs,in_size,out_size,activation_function=None):
    with tf.name_scope('layer'):
        with tf.name_scope('weights'):
            Weights = tf.Variable(tf.random_normal([in_size,out_size]),name='W')
        with tf.name_scope('biases'):
            biases = tf.Variable(tf.zeros([1,out_size]) + 0.1,name='b')#初始值不推荐为0
        with tf.name_scope('Wx_plus_b'):
            Wx_plus_b = tf.add(tf.matmul(inputs,Weights),biases)
        if activation_function is None:
            outputs = Wx_plus_b
        else:
            outputs = activation_function(Wx_plus_b)
        return outputs

with tf.name_scope('inputs'):#这一句可以把xs和ys包含进来，形成一个大的图层，图层的名字就是里面的参数
    xs = tf.placeholder(tf.float32,[None,1],name='x_in')# 这里制定的名称将来会在可视化图层inputs中显示出来
    ys = tf.placeholder(tf.float32,[None,1],name='y_in')

with tf.name_scope('loss'):
    loss = tf.reduce_mean(tf.reduce_sum(tf.square(ys - prediction),
                     reduction_indices=[1]))

with tf.name_scope('train'):
    train_step = tf.train.GradientDescentOptimizer(0.1).minimize(loss)

# 我们需要使用 tf.summary.FileWriter(), 将上面‘绘画’出的图保存到一个目录中，以方便后期在浏览器中可以浏览。 这个方法中的第二个参数需要使用sess.graph ， 因此我们需要把这句话放在获取session的后面。 这里的graph是将前面定义的框架信息收集起来，然后放在logs/目录下面。
writer = tf.summary.FileWriter("logs/", sess.graph)
```

完整代码：

```python
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt

def add_layer(inputs,in_size,out_size,activation_function=None):
    with tf.name_scope('layer'):
        with tf.name_scope('weights'):
            Weights = tf.Variable(tf.random_normal([in_size,out_size]),name='W')
        with tf.name_scope('biases'):
            biases = tf.Variable(tf.zeros([1,out_size]) + 0.1,name='b')#初始值不推荐为0
        with tf.name_scope('Wx_plus_b'):
            Wx_plus_b = tf.add(tf.matmul(inputs,Weights),biases)
        if activation_function is None:
            outputs = Wx_plus_b
        else:
            outputs = activation_function(Wx_plus_b)
        return outputs

x_data = np.linspace(-1,1,300,dtype=np.float32)[:,np.newaxis]
noise = np.random.normal(0,0.05,x_data.shape)
y_data = np.square(x_data) - 0.5 + noise

with tf.name_scope('inputs'):#这一句可以把xs和ys包含进来，形成一个大的图层，图层的名字就是里面的参数
    xs = tf.placeholder(tf.float32,[None,1],name='x_in')# 这里制定的名称将来会在可视化图层inputs中显示出来
    ys = tf.placeholder(tf.float32,[None,1],name='y_in')

# 搭建神经网络
lay1 = add_layer(xs,1,10,activation_function=tf.nn.relu)
prediction = add_layer(lay1,10,1,activation_function=None)
# 计算预测值prediction和真实值的误差，对二者差的平方求和再取平均
with tf.name_scope('loss'):
    loss = tf.reduce_mean(tf.reduce_sum(tf.square(ys - prediction),
                     reduction_indices=[1]))
# 如何让机器学习提升它的准确率。tf.train.GradientDescentOptimizer()中的值通常都小于1，这里取的是0.1，代表以0.1的效率来最小化误差loss。
with tf.name_scope('train'):
    train_step = tf.train.GradientDescentOptimizer(0.1).minimize(loss)
# 使用变量之前先初始化
init = tf.global_variables_initializer()
# 定义session
sess = tf.Session()

# 我们需要使用 tf.summary.FileWriter(), 将上面‘绘画’出的图保存到一个目录中，以方便后期在浏览器中可以浏览。 这个方法中的第二个参数需要使用sess.graph ， 因此我们需要把这句话放在获取session的后面。 这里的graph是将前面定义的框架信息收集起来，然后放在logs/目录下面。
writer = tf.summary.FileWriter("logs/", sess.graph)
# 只有session.run()才会执行我们定义的运算
sess.run(init)

# 可视化
fig = plt.figure()
ax = fig.add_subplot(1,1,1)
ax.scatter(x_data,y_data)
plt.ion()#使程序plot之后不暂停，是显示动图的关键

# 训练1000次
for i in range(1000):
    sess.run(train_step,feed_dict={xs:x_data,ys:y_data})
    # 每50步输出一下机器学习的误差
    if i % 50 == 0:
        # print(sess.run(loss,feed_dict={xs:x_data,ys:y_data}))
        try:
            ax.lines.remove(lines[0])
        except Exception:
            pass
        prediction_value = sess.run(prediction,feed_dict={xs:x_data})
        lines = ax.plot(x_data,prediction_value,'r-',lw=5)
        plt.pause(0.1)
plt.show()  
```

在anaconda下的python虚拟环境中，输入tensorboard指令报错，需要先激活tensorflow。打开Anaconda Prompt，在该目录下面输入`activate tensorflow`，然后再输入`tensorboard --logdir logs`命令即可。

## 可视化神经网络的训练过程

### 在 layer 中为 Weights, biases 设置变化图表

`tf.histogram_summary()`方法,用来绘制图片, 第一个参数是图表的名称, 第二个参数是图表要记录的变量

```python
# 为每一层添加名字参数
def add_layer(inputs,in_size,out_size,n_layer,activation_function=None):
    layer_name = 'layer%s'%n_layer
    with tf.name_scope('layer'):
        with tf.name_scope('weights'):
            Weights = tf.Variable(tf.random_normal([in_size,out_size]),name='W')
            # 采用tf.histogram_summary()方法,用来绘制图片, 第一个参数是图表的名称, 第二个参数是图表要记录的变量
             tf.summary.histogram(layer_name + '/weights', Weights) # tensorflow >= 0.12
        with tf.name_scope('biases'):
            biases = tf.Variable(tf.zeros([1,out_size]) + 0.1,name='b')#初始值不推荐为0
             tf.summary.histogram(layer_name + '/biases', biases) # tensorflow >= 0.12
        with tf.name_scope('Wx_plus_b'):
            Wx_plus_b = tf.add(tf.matmul(inputs,Weights),biases)
        if activation_function is None:
            outputs = Wx_plus_b
        else:
            outputs = activation_function(Wx_plus_b)
         tf.summary.histogram(layer_name + '/outputs', outputs) # tensorflow >= 0.12
        return outputs
# 添加n_layer参数
lay1 = add_layer(xs,1,10,n_layer=1,activation_function=tf.nn.relu)
prediction = add_layer(lay1,10,1,n_layer=2,activation_function=None)
```

### 设置loss的变化图

Loss 的变化图和之前设置的方法略有不同. loss是在tesnorBorad 的event下面的, 这是由于我们使用的是tf.scalar_summary() 方法.

```python
with tf.name_scope('loss'):
    loss = tf.reduce_mean(tf.reduce_sum(tf.square(ys - prediction),
                     reduction_indices=[1]))
    tf.summary.scalar('loss', loss) # tensorflow >= 0.12
```

### 给所有的训练图合并
`tf.merge_all_summaries()` 方法会对我们所有的 summaries 合并到一起. 因此在原有代码片段中添加：

```python
# 定义session
sess = tf.Session()
# 合并变量
merged = tf.summary.merge_all() # tensorflow >= 0.12
# 我们需要使用 tf.summary.FileWriter(), 将上面‘绘画’出的图保存到一个目录中，以方便后期在浏览器中可以浏览。 这个方法中的第二个参数需要使用sess.graph ， 因此我们需要把这句话放在获取session的后面。 这里的graph是将前面定义的框架信息收集起来，然后放在logs/目录下面。
writer = tf.summary.FileWriter("logs/", sess.graph)
# 只有session.run()才会执行我们定义的运算
sess.run(tf.global_variables_initializer())
```

### 训练数据
原有的代码仅仅可以记录很绘制出训练的图表， 但是不会记录训练的数据。 为了较为直观显示训练过程中每个参数的变化，我们每隔上50次就记录一次结果 , 同时我们也应注意, merged 也是需要run 才能发挥作用的，修改的代码是：

```python
# 训练1000次
for i in range(1000):
    sess.run(train_step,feed_dict={xs:x_data,ys:y_data})
    # 每50步输出一下机器学习的误差
    if i % 50 == 0:
        # print(sess.run(loss,feed_dict={xs:x_data,ys:y_data}))
        try:
            ax.lines.remove(lines[0])
        except Exception:
            pass
        rs = sess.run(merged,feed_dict={xs:x_data,ys:y_data})
        writer.add_sumary(rs,i)
        prediction_value = sess.run(prediction,feed_dict={xs:x_data})
        lines = ax.plot(x_data,prediction_value,'r-',lw=5)
        plt.pause(0.1)
```

### 显示最终效果

（这里还有问题，没有如预想）

完整代码

```python
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt

# 为每一层添加名字参数
def add_layer(inputs,in_size,out_size,n_layer,activation_function=None):
    layer_name = 'layer%s'%n_layer
    with tf.name_scope('layer'):
        with tf.name_scope('weights'):
            Weights = tf.Variable(tf.random_normal([in_size,out_size]),name='W')
            # 采用tf.histogram_summary()方法,用来绘制图片, 第一个参数是图表的名称, 第二个参数是图表要记录的变量
            tf.summary.histogram(layer_name+'/weights',Weights)  # tensorflow >= 0.12
        with tf.name_scope('biases'):
            biases = tf.Variable(tf.zeros([1,out_size]) + 0.1,name='b')#初始值不推荐为0
            tf.summary.histogram(layer_name + '/biases', biases) # tensorflow >= 0.12
        with tf.name_scope('Wx_plus_b'):
            Wx_plus_b = tf.add(tf.matmul(inputs,Weights),biases)
        if activation_function is None:
            outputs = Wx_plus_b
        else:
            outputs = activation_function(Wx_plus_b)
        tf.summary.histogram(layer_name + '/outputs', outputs) # tensorflow >= 0.12
        return outputs

x_data = np.linspace(-1,1,300,dtype=np.float32)[:,np.newaxis]
noise = np.random.normal(0,0.05,x_data.shape)
y_data = np.square(x_data) - 0.5 + noise

with tf.name_scope('inputs'):#这一句可以把xs和ys包含进来，形成一个大的图层，图层的名字就是里面的参数
    xs = tf.placeholder(tf.float32,[None,1],name='x_in')# 这里制定的名称将来会在可视化图层inputs中显示出来
    ys = tf.placeholder(tf.float32,[None,1],name='y_in')

# 搭建神经网络
lay1 = add_layer(xs,1,10,n_layer=1,activation_function=tf.nn.relu)
prediction = add_layer(lay1,10,1,n_layer=2,activation_function=None)
# 计算预测值prediction和真实值的误差，对二者差的平方求和再取平均
with tf.name_scope('loss'):
    loss = tf.reduce_mean(tf.reduce_sum(tf.square(ys - prediction),
                     reduction_indices=[1]))
    tf.summary.scalar('loss', loss) # tensorflow >= 0.12
# 如何让机器学习提升它的准确率。tf.train.GradientDescentOptimizer()中的值通常都小于1，这里取的是0.1，代表以0.1的效率来最小化误差loss。
with tf.name_scope('train'):
    train_step = tf.train.GradientDescentOptimizer(0.1).minimize(loss)

# 定义session
sess = tf.Session()
# 合并变量
merged = tf.summary.merge_all() # tensorflow >= 0.12
# 我们需要使用 tf.summary.FileWriter(), 将上面‘绘画’出的图保存到一个目录中，以方便后期在浏览器中可以浏览。 这个方法中的第二个参数需要使用sess.graph ， 因此我们需要把这句话放在获取session的后面。 这里的graph是将前面定义的框架信息收集起来，然后放在logs/目录下面。
writer = tf.summary.FileWriter("logs/", sess.graph) # tensorflow >=0.12
# 只有session.run()才会执行我们定义的运算
sess.run(tf.global_variables_initializer())

# 可视化
fig = plt.figure()
ax = fig.add_subplot(1,1,1)
ax.scatter(x_data,y_data)
plt.ion()#使程序plot之后不暂停，是显示动图的关键

# 训练1000次
for i in range(1000):
    sess.run(train_step,feed_dict={xs:x_data,ys:y_data})
    # 每50步输出一下机器学习的误差
    if i % 50 == 0:
        # print(sess.run(loss,feed_dict={xs:x_data,ys:y_data}))
        try:
            ax.lines.remove(lines[0])
        except Exception:
            pass
        rs = sess.run(merged,feed_dict={xs:x_data,ys:y_data})
        writer.add_sumary(rs,i)
        prediction_value = sess.run(prediction,feed_dict={xs:x_data})
        lines = ax.plot(x_data,prediction_value,'r-',lw=5)
        plt.pause(0.1)
plt.show()  
```

# 高阶内容

## 分类学习Classification









000
