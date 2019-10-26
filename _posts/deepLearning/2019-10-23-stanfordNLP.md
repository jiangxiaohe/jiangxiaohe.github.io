---
layout: post
title: NLP分词、命名实体识别、关系识别
tags:
- NLP
categories: deepLearning
description: THULAC、pynlpir、StanfordNLP
---

# [THULAC](http://thulac.thunlp.org/)

THU Lexical(词法) Analyzer for Chinese。该工具包具有中文分词和词性标注功能。

* 下载。`pip install thulac`

* 词性标注，共有以下类型的词性标注。

```
n/名词 np/人名 ns/地名 ni/机构名 nz/其它专名
m/数词 q/量词 mq/数量词 t/时间词 f/方位词 s/处所词
v/动词 a/形容词 d/副词 h/前接成分 k/后接成分 i/习语
j/简称 r/代词 c/连词 p/介词 u/助词 y/语气助词
e/叹词 o/拟声词 g/语素 w/标点 x/其它
```

* 使用举例

```python
nlp = thulac.thulac(filt=True)
text = '我爱北京天安门'
nlp.cut(text,text=False) # text默认False，返回一个二维数组([[word, tag]..]),seg_only模式下tag为空字符。
nlp.cut(text,text=True) # 返回字符串'我/r 爱/vm 北京/ns 天安门/ns'
```

# [thunlp/OpenNRE](https://github.com/thunlp/OpenNRE)

实体关系抽取entity relation extraction。

本开源工具只能用作训练，没有预训练模型，需要自己训练。

# [StanfordNLP]()
