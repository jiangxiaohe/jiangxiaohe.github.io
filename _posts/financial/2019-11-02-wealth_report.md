---
layout: post
title: 从东方财富网爬取上市公司财务报表
tags:
- 爬虫
categories: financial
description: 从东方财富网爬取上市公司财务报表,以贵州茅台（600519）为例
---

# 需求分析

很多网站都提供上市公司的公告、财务报表等金融投资信息和数据，比如：腾讯财经、网易财经、新浪财经、东方财富网等。这之中，发现东方财富网的数据非常齐全。东方财富网有一个[数据中心](ttp://data.eastmoney.com/center/)，该数据中心提供包括特色数据、研究报告、年报季报等在内的大量数据.

# 获取业绩报表API

首先在网站上左侧选择业绩报表，然后搜索贵州茅台（600519）的业绩报表，进入[业绩报表明细](http://data.eastmoney.com/bbsj/yjbb/600519.html)，可以看到该分类下又包括：业绩报表、业绩快报、利润表等7个报表的数据。

点击下一页，可以看到表格更新后url没有发生改变，可以判定是采用了Javscript。那么，我们首先判断是不是采用了Ajax加载的。方法也很简单，右键检查或按F12，切换到network并选择下面的XHR，再按F5刷新。可以看到只有一个Ajax请求，点击下一页也并没有生成新的Ajax请求，可以判断该网页结构不是常见的那种点击下一页或者下拉会源源不断出现的Ajax请求类型，那么便无法构造url来实现分页爬取。

XHR选项里没有找到我们需要的请求，接下来试试看能不能再JS里找到表格的数据请求。将选项选为JS，再次F5刷新，可以看到出现了很多JS请求，然后我们点击几次下一页，会发现弹出新的请求来，然后右边为响应的请求信息。url链接非常长，看上去很复杂。

以上参考[Python中文社区](https://blog.csdn.net/BF02jgtRS00XKtCx/article/details/83110243)

在控制台network下选择js，点击下一页，可以看到新来的js请求，选择时间段来方便找到改js请求。可以找到：

Headers中的requestURL为

`http://dcfm.eastmoney.com//em_mutisvcexpandinterface/api/js/get?type=YJBB21_YJBB&token=70f12f2f4f091e459a279469fe49eca5&filter=(scode=600519)&st=reportdate&sr=-1&p=2&ps=50&js=var%20Ayfvpdga={pages:(tp),data:%20(x),font:(font)}&rt=52422852`

Preview下的data即为所需股票数据。可以看到scode部分是股票代码，p=2显示页数，将p改为1然后可以看到顺利返回第一页的表格数据，说明api分析正确。

至于其他参数是否可以省略，需要自己尝试。比如去除token参数后获取不到数据，则该参数不能去除。最后的rt、js参数去除后并不影响数据获取，则可以去除。


# 用urllib库获取api返回数据

urllib是Python中请求url连接的官方标准库.urllib中一共有四个模块，分别如下：

* request：主要负责构造和发起网络请求,定义了适用于在各种复杂情况下打开 URL (主要为 HTTP) 的函数和类
* rror：处理异常
* parse：解析各种数据格式
* robotparser：解析robot.txt文件

```python
import urllib3
url = 'http://dcfm.eastmoney.com//em_mutisvcexpandinterface/api/js/get?type=YJBB21_YJBB&token=70f12f2f4f091e459a279469fe49eca5&filter=(scode={})&st=reportdate&sr=-1&p={page}&ps={pageSize}%20Ayfvpdga={pages:(tp),data:%20(x),font:(font)}'

# 创建网络请求连接池
conn_pool = urllib3.PoolManager()

# 设置要抓取数据的股票代码
code = '600519'

# 发送网络请求，获取返回结果
response = conn_pool.request('GET', url.replace('{0}', code))

# 对返回结果中的数据进行解码
result = response.data.decode('UTF-8')
```

# 提取数据，并且保存到文件

```python
# 解析抓取结果
result = json.loads(result)

# 取出业绩报表相关的数据
reports = result['data']

# 转换成DataFrame数据类型，保存到csv文件中
reports = pd.DataFrame(reports)

# 将提取的数据保存到文件中
reports.to_csv(code+'.csv',encoding='utf-8-sig')
```




