---
layout: post
title: kaggle实战 titanic
tags:
categories: deepLearning
description:
---

[官方比赛网址](https://www.kaggle.com/c/titanic)

[主要参考解答](https://blog.csdn.net/Koala_Tree/article/details/78725881)

# 数据总览和预处理

## 数据预处理

主要需要处理的是缺失值，包括训练集和测试集。通过数据信息可以看到，train的Age、Cabin、Embarked有缺失，test的Age、Cabin、Embarked、Fare有缺失（其中Fare只有一项缺失）。

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 12 columns):
PassengerId    891 non-null int64
Survived       891 non-null int64
Pclass         891 non-null int64
Name           891 non-null object
Sex            891 non-null object
Age            714 non-null float64
SibSp          891 non-null int64
Parch          891 non-null int64
Ticket         891 non-null object
Fare           891 non-null float64
Cabin          204 non-null object
Embarked       889 non-null object
dtypes: float64(2), int64(5), object(5)
memory usage: 83.6+ KB
----------------------------------------
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 418 entries, 0 to 417
Data columns (total 11 columns):
PassengerId    418 non-null int64
Pclass         418 non-null int64
Name           418 non-null object
Sex            418 non-null object
Age            332 non-null float64
SibSp          418 non-null int64
Parch          418 non-null int64
Ticket         418 non-null object
Fare           417 non-null float64
Cabin          91 non-null object
Embarked       418 non-null object
dtypes: float64(2), int64(4), object(5)
memory usage: 36.0+ KB
```

* 如果数据集很多，但有很少的缺失值，可以删掉带缺失值的行；
* 如果该属性相对学习来说不是很重要，可以对缺失值赋均值或者众数。比如在哪儿上船Embarked这一属性（共有三个上船地点），缺失俩值，可以用众数赋值
* 对于标称属性，可以赋一个代表缺失的值，比如‘U0’。因为缺失本身也可能代表着一些隐含信息。比如船舱号Cabin这一属性，缺失可能代表并没有船舱。
* 使用回归 随机森林等模型来预测缺失属性的值。因为Age在该数据集里是一个相当重要的特征（先对Age进行分析即可得知），所以保证一定的缺失值填充准确率是非常重要的，对结果也会产生较大影响。一般情况下，会使用数据完整的条目作为模型的训练集，以此来预测缺失值。对于当前的这个数据，可以使用随机森林来预测也可以使用线性回归预测。这里使用随机森林预测模型，选取数据集中的数值属性作为特征（因为sklearn的模型只能处理数值属性，所以这里先仅选取数值特征，但在实际的应用中需要将非数值特征转换为数值特征）

# 分析数据关系

可以分析的关系有：
* 是否生存和单因素（或者多因素）的关系：性别、船舱等级、年龄、

需要熟练使用pandas中的groupby函数

# 变量转换

对于一些非数值信息进行处理。

## 定性转换
* Dummy Variables：qualitative variable是一些频繁出现的几个独立变量时，Dummy Variables比较适合使用。即one-hot形式
* Factorizing：dummy不好处理Cabin（船舱号）这种标称属性，因为他出现的变量比较多。所以Pandas有一个方法叫做factorize()，它可以创建一些数字，来表示类别变量，对每一个类别映射一个ID，这种映射最后只生成一个特征，不像dummy那样生成多个特征。

## 定量转换
* Scaling可以将一个很大范围的数值映射到一个很小的范围(通常是-1 - 1，或则是0 - 1)，很多情况下我们需要将数值做Scaling使其范围大小一样，否则大范围数值特征将会由更高的权重。比如：Age的范围可能只是0-100，而income的范围可能是0-10000000，在某些对数组大小敏感的模型中会影响其结果。
* Binning通过观察“邻居”(即周围的值)将 **连续数据离散化**。存储的值被分布到一些“桶”或“箱“”中，就像直方图的bin将数据划分成几块一样。
* 在将数据Bining化后，要么将数据factorize化，要么dummies化。

# 特征工程
在进行特征工程的时候，我们不仅需要对训练数据进行处理，还需要同时将测试数据同训练数据一起处理，使得二者具有相同的数据类型和数据分布。

对每一列分别进行处理。
```
列             处理方式
PassengerId    891 non-null int64
Survived       891 non-null int64
Pclass         one-hot编码，即dummy处理
Name           从名字中提取称呼，将各类称呼进行统一化处理，使用dumpy对不同称呼分类。
Sex            one-hot编码，即dummy处理
Age            用机器学习方法填充
SibSp          经过之前的分析，此项无大影响
Parch          经过之前的分析，此项无大影响
Ticket         891 non-null object
Fare           从船舱的等级的平均票价预测
Cabin          204 non-null object
Embarked       缺失值不多，用众数填充
```

对于age项的补充：因为Age项的缺失值较多，所以不能直接填充age的众数或者平均数。常见的有两种对年龄的填充方式：一种是根据Title中的称呼，如Mr，Master、Miss等称呼不同类别的人的平均年龄来填充；一种是综合几项如Sex、Title、Pclass等其他没有缺失值的项，使用机器学习算法来预测Age。这里我们使用后者来处理。以Age为目标值，将Age完整的项作为训练集，将Age缺失的项作为测试集。







0
