---
layout: post
title: 图数据库neo4j
tags:
categories: database
description: 
---

随着社交、电商、金融、零售、物联网等行业的快速发展，现实社会织起了了一张庞大而复杂的关系网，传统数据库很难处理关系运算。

关系型数据库实际上是不擅长处理关系的。很多场景下，你的业务需求完全超出了当前的数据库架构。
假设某关系型数据库中有这么几张用户、订单、商品表，订单表中的订单id将用户id对应起来，用户和商品的详细信息分别在用户表和商品表中。

当我们要查询：“用户购买了那些商品？” 或者 “该商品有哪些客户购买过？” 需要开发人员JOIN几张表，效率非常低下。

而“购买该产品的客户还购买了哪些商品？”类似的查询几乎不可能实现。

neo4j社区版开源，非商用免费，商业版支持HA集群，并不是完全分布式，使用最广最流行.

[官方文档](http://neo4j.com.cn/public/docs/)

## 下载安装

安装neo4j前需要配置java jdk。用

在[官网](https://neo4j.com/download-center/#enterprise)下载，不需要选择桌面版，选择下面的`community server`版本，我这里下载的版本为`neo4j-community-3.5.11-unix.tar.gz`.官网下载太慢了，在csdn上找到了这个镜像。

按照下载页面的方式解压安装
1. tar -xf filename -C /usr/local/neo4j
   1. 解压后有四个主要目录
   2. bin目录：neo4j的基本执行程序，如应用的开启与关闭，cypher语句的操作等等
   3. conf目录：neo4j的配置文件
   4. data目录：数据的管理
   5. import目录：文件导入目录。如将该目录下的对应csv文件导入neo4j数据库
2. 修改配置文件`conf/neo4j.conf`，找到#dbms.connectors.default_listen_address=0.0.0.0，将注释去掉（该操作为允许http接口远程访问neo4j服务器）
3. 进入bin目录，启动服务`./neo4j start`
   1. 可以看到neo4j已经启动（pid 28224）， It is available at http://0.0.0.0:7474/
   2. 默认的host是bolt://localhost:7687，如果远程访问，需要打开7474和7687两个端口。
   3. 在网页进入该端口，初始用户名密码均为neo4j，进入后修改密码
   4. 这时候可在`localhost:7474`访问，也可在局域网访问
4. 也可以将这些命令添加到用户环境变量，在`.bashrc`文件后添加如下代码`export PATH=/usr/local/neo4j/bin:$PATH`，然后`source`一下即可。

## 探索样本数据集

* `:play movie graph`展示创建查询数据库的基本操作：
  * create创建图
  * find：检索单个电影和演员
  * query：发现相关的演员和导演
  * solve：Bacon Path
  * Recommend：
  * clean up：删除数据
* `:play northwind graph`