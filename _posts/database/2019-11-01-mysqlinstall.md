---
layout: post
title: 数据库系统搭建
tags:
categories: database
description: 在服务器搭建数据库并且实现远程连接
---

# 搭建mysql数据库

* 安装：`apt-get install mysql-server`
* 设置：`service mysql start/stop/restart`
* 查看mysql进程:`ps -ef | grep mysql`
* 查看mysql监听的端口:`netstat -tap | grep mysql`
* 修改配置文件：`vim /etc/mysql/my.cnf`

21分钟 MySQL 入门教程
https://www.cnblogs.com/mr-wid/archive/2013/05/09/3068229.html#d2