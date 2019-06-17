---
layout: post
title: 图片去重
tags:
categories: deepLearning
description: 图片如何去重？
---


应用于图片网站的版权保护，在用户上传图片时与媒体库中的图片进行比较，如果检测出该图片已存在，就可以报错，并标记该用户欺诈。

在大量文件中检测某文件是否已经存在的一个常用方法是，通过计算数据集中每一个文件的哈希值，并将该哈希值存储在数组库中。当想要查找某特定文件时，首先计算该文件哈希值，然后在数据库中查找该哈希值。

md5方法对于文件的任何细微改变都会导致hash值的不同，不用于图片去重，常用的方法是dhash。

# 基于dhash的图片去重

https://blog.csdn.net/Gentle_Guan/article/details/73384767

1. 通过listdir列出目录内文件，通过后缀集合(postFix)进行图片格式检查
2. 每个图片转换为灰度图片并压缩大小(8,9) 便于处理
3. 得到每个像素的value(0-255)
4. 每一个像素点与它左边的像素点进行比较，得到0或者1
5. 比较结果存入diff后放在allDiff中
6. 暴力比较每两张图片的汉明距离
7. 设定汉明距离的范围，得出重复图片

```python
width = 32
high = 32  # 压缩后的大小
dirName = ""  # 相册路径
allDiff = []
postFix = picPostfix()  #  图片后缀的集合

dirList = listdir(dirName)
cnt = 0
for i in dirList:
    cnt += 1
    print cnt  # 可以不打印 表示处理的文件计数
    if str(i).split('.')[-1] in postFix:  # 判断后缀是不是照片格式
        im = Image.open(r'%s\%s' % (dirName, unicode(str(i), "utf-8")))
        diff = getDiff(width, high, im)
        allDiff.append((str(i), diff))
# 暴力判断是否是相同图片
for i in range(len(allDiff)):
    for j in range(i + 1, len(allDiff)):
        if i != j:
            ans = getHamming(allDiff[i][1], allDiff[j][1])
            if ans <= 5:  # 判别的汉明距离，自己根据实际情况设置
                print allDiff[i][0], "and", allDiff[j][0], "maybe same photo..."

```

# 海量数据去重之SimHash算法

https://blog.csdn.net/u010454030/article/details/49102565

SimHash是Google在2007年发表的论文《Detecting Near-Duplicates for Web Crawling 》中提到的一种指纹生成算法或者叫指纹提取算法，被Google广泛应用在亿级的网页去重的Job中，作为locality sensitive hash（局部敏感哈希）的一种，其主要思想是降维。

一篇若干数量的文本内容，经过simhash降维后，可能仅仅得到一个长度为32或64位的二进制由01组成的字符串，对该文本的simhash码过程请查看原文，主要步骤如下：
* 清洗+hash、关键词hash、关键词向量加权建立、合并向量累加、降维得指纹
* 得到指纹后通过汉明距离比较文本相似度。在google的论文给出的数据中，64位的签名，在海明距离为3的情况下，可认为两篇文档是相似的或者是重复的，当然这个值只是参考值，针对自己的应用可能又不同的测试取值

到这里相似度问题基本解决，但是按这个思路，在海量数据几百亿的数量下，效率问题还是没有解决的，因为数据是不断添加进来的，不可能每来一条数据，都要和全库的数据做一次比较，按照这种思路，处理速度会越来越慢，线性增长。

针对海量数据的去重效率，我们可以将64位指纹，切分为4份16位的数据块，根据抽屉原理在海明距离为3的情况，如果两个文档相似，那么它必有一个块的数据是相等的。

然后将4份数据通过K-V数据库或倒排索引存储起来K为16位截断指纹，V为K相等时剩余的48位指纹集合，查询时候，精确匹配这个指纹的4个16位截断。

> 我们用dhash的64位hash串，汉明距离在5位及以下可认为重复，采用simhash算法，分成六块。

# 相似搜索

相似搜索的共有三大类方法：
* 基于树的方法
	* KD树是其下的经典算法。一般而言，在空间维度比较低时，KD树的查找性能还是比较高效的；但当空间维度较高时，该方法会退化为暴力枚举，性能较差，这时一般会采用下面的哈希方法或者矢量量化方法。
* 哈希方法
	* LSH(Locality-Sensitive Hashing)是其下的代表算法。文献[7]是一篇非常好的LSH入门资料。
	* 对于小数据集和中规模的数据集(几个million-几十个million)，基于LSH的方法的效果和性能都还不错。这方面有2个开源工具FALCONN和NMSLIB。
* 矢量量化方法
	* 矢量量化方法，即vector quantization。在矢量量化编码中，关键是码本的建立和码字搜索算法。比如常见的聚类算法，就是一种矢量量化方法。而在相似搜索中，向量量化方法又以PQ方法最为典型。
	* 对于大规模数据集(几百个million以上)，基于矢量量化的方法是一个明智的选择，可以用用Faiss开源工具。

# 局部敏感哈希(Locality-Sensitive Hashing, LSH)
https://blog.csdn.net/chichoxian/article/details/80290782

* 怎么发现“相似”的集合或者项在一个非常大的集合里而不需要一个一个的比较（两两对比）。因为这样的比较是一个二次方的时间复杂度。
* Locality Sensitive Hashing(LSH) 一般的思想就是hash items （项）到一个桶里（bins）很多次，并且留意在同一个bin 里的items。
* 仅仅只有那些高相似度的items 有更多的可能在同一个桶里。

许多的数据挖掘问题都可以表示为发现相似的集合的问题：
* 网页有很多的相似的词汇，可以用来根据主题来分类
* 电影的推荐系统
* 根据某个电影找到喜欢同类电影的人

相似的文档例子
* 镜像网站（mirror sites）
* 剽窃问题 包括大量的引用
* 相似的新闻在很多的不同的网站

shingling:可以理解为对一个文档进行切割
minhashing:将一个大的集合中的元素转换为很多短小的签名，但是保持了这些集合中的元素的相似性

# 乘积量化PQ(product quantization) 算法

https://blog.csdn.net/mydear_11000/article/details/83749059

意思是指把原来的向量空间分解为若干个低维向量空间的笛卡尔积，并对分解得到的低维向量空间分别做量化（quantization）。这样每个向量就能由多个低维空间的量化code组合表示。

该方法用于解决相似搜索问题、或者说近邻搜索问题。PQ方法是高效相似搜索方法的一种。


https://www.cnblogs.com/mafuqiang/p/7161592.html



0
