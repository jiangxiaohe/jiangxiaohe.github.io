---
layout: post
title: 抓包工具wireshark和fiddler
tags:
categories: tools
description: 抓包工具使用
---

# [wireshark常用过滤选项](https://www.cnblogs.com/nmap/p/6291683.html)

* 过滤源ip、目的ip

在wireshark的过滤规则框Filter中输入过滤条件。如查找目的地址为192.168.101.8的包，ip.dst==192.168.101.8；查找源地址为ip.src==1.1.1.1

* 端口过滤

如过滤80端口，在Filter中输入，tcp.port==80，这条规则是把源端口和目的端口为80的都过滤出来。使用tcp.dstport==80只过滤目的端口为80的，tcp.srcport==80只过滤源端口为80的包

* 协议过滤

比较简单，直接在Filter框中直接输入协议名即可，如过滤HTTP的协议

* http模式过滤

如过滤get包，http.request.method=="GET",过滤post包，http.request.method=="POST"

* 连接符and的使用

过滤两种条件时，使用and连接，如过滤ip为192.168.101.8并且为http协议的，ip.src==192.168.101.8 and http

# [fiddler教程](https://www.jianshu.com/p/99b6b4cd273c)

Fiddler（中文名称：小提琴）是一个HTTP的调试代理，以代理服务器的方式，监听系统的Http网络数据流动，Fiddler可以也可以让你检查所有的HTTP通讯，设置断点，以及Fiddle所有的“进出”的数据（一般用来抓包）,Fiddler还包含一个简单却功能强大的基于JScript .NET事件脚本子系统，它可以支持众多的HTTP调试任务。

Fiddler 是以代理web服务器的形式工作的,它使用代理地址:127.0.0.1, 端口:8888. 当Fiddler会自动设置代理， 退出的时候它会自动注销代理，这样就不会影响别的程序。

如果要捕捉chrome浏览器的http请求，需要设置浏览器代理，设置代理协议http，地址为127.0.0.1，端口号8888。

Fiddler的主界面分为 工具面板、会话面板、监控面板、状态面板

* 工具面板

依次是：说明注释、重新请求、删除会话、继续执行、流模式/缓冲模式、解码、保留会话、监控指定进程、寻找、保存会话、切图、计时、打开浏览器、清除IE缓存、编码/解码工具、弹出控制监控面板、MSDN、帮助。

两种模式
* 缓冲模式（Buffering Mode）Fiddler直到HTTP响应完成时才将数据返回给应用程序。可以控制响应，修改响应数据。但是时序图有时候会出现异常
* 流模式（Streaming Mode）Fiddler会即时将HTTP响应的数据返回给应用程序。更接近真实浏览器的性能。时序图更准确,但是不能控制响应。

* 会话面板
* 监控面板

重点关注过滤选项：
比如设置只过滤微信消息，需要在hosts栏设置mp.weixin.qq.com;然后点击actions按钮启动过滤器。

* 移动端抓包

该方案未实现。
