---
layout: post
title: 阿里云域名购买和域名解析
tags:
categories: aliyun
description:
---
# 阿里云域名注册步骤
参考[阿里云通用域名注册操作指导](https://help.aliyun.com/document_detail/54068.html)
主要步骤有：
* 购买域名
* 填写个人信息
* 实名认证
* 设置域名解析

[设置域名解析](https://www.jianshu.com/p/6e1bd87f9e9a)：
1. 获取github pages的ip地址：`ping jiangxiaohe.github.io`
2. 配置阿里云域名解析->新手导引->填写刚才获取的ipv4地址
3. 配置github pages的custom domain
	* 进入你的github pages的仓库setting，然后在设置里面将的你的域名的地址，添加到custom domain中，然后保存即可。
4. 完成，可能要滞后一段时间才生效
