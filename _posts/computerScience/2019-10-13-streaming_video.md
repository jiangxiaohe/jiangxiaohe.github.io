---
layout: post
title: 流媒体技术
tags:
categories: computerScience
description:
---

[TOC]

# 第一章 流媒体概述 （2学时）

连续媒体（视频/音频）经压缩编码、数据打包后按照一定的时间间隔要求连续地发送给接收方设备，接收方设备在后续数据不断到达的同时对接收到的数据进行重组、解码和播放。

传输模式是“边传输边播放”，初始等待时间很短，无序磁盘缓存或者需要量很少。

流媒体的特性：
* 端到端延时约束
  * 流媒体点播
    * 起始延时：<10s
    * 类VCR操作（例如拖动进度条）：<1~2s
  * 流媒体直播
    * 可容忍几十秒的延时
  * 交互式会话
    * <150ms  good
    * <400ms  OK
* 时序性约束
  * 流媒体数据必须按照一定的顺序连续播放
* 一定程度的容错性

流媒体面临的挑战：
* 流媒体数据量庞大，必须进过压缩编码后才能在网络上传输和存储
  * 压缩的视频数据在解码时存在数据依赖性问题
    * 帧内依赖
    * 帧间依赖I，P，B
  * 压缩的视频数据，其码率可能随视频场景的变化而动态波动
  * 实时性约束
* IP网络
  * 基于分组交换的无连接网络
    * IP分组在一系列的路由器之间进行存储转发
    * 可能因为路由器缓冲区溢出而发生分组丢失
  * 尽力而为
    * 端到端带宽、时延等均不能保证
  * 常用传输协议
    * UDP
    * TCP
      * 慢启动
      * 拥塞避免
  * TCP友好
* 支持大规模用户：组播
* 网络与用户端设备的异构性
* 无线和移动网络的问题
  * “隐藏”节点问题：由于距离太远，一个节点无法检测到媒体竞争对手的存在
  * 由于侦听到其他站点的发送而误以为区域忙导致不能发送

当前的Internet体系结构只能提供“尽力服务”（Best-Effort Service），不能严格保障视频播放所需要的传输服务质量（Quality of Service，简称QoS），因此，如何在“尽力服务”的网络传输条件下获得良好的视频播放质量成为了一个重大的挑战。

流媒体服务质量要素：
画质、启动延迟、平滑、交互性。

对于视频数据而言，数据的到达需要满足严格的时间间隔才能保证视频的连续播放。当网络拥塞发生时，数据的传输会经历漫长的排队等候时间，客户端的播放因为等待数据而不得不暂停，造成播放质量的严重下降。

# 第二章 流媒体用户体验测量与建模（2学时）

视频表现
1. Startup Delay:Video starts without much delay.
2. Rebuffers:Video plays witho· t freezes.
3. Availability:Viewers download video withou failure.

用户行为（一一对应）
1. Abandonment:Reduce viewers who abandon without viewing the video.
2. Engagement:Viewers watch videos longer.
3. Repeat Viewership:Viewers keep coming back to site to watch more videos.

第三章 面向网络的多媒体编解码技术 （2学时）

第四章 端到端流媒体网络传输与动态自适应 （8学时）

第五章 P2P流媒体技术（2学时）

第六章 内容分发网络 （4学时）

第七章 无线网络流媒体（2学时）

第八章 AI辅助的流媒体传输（4学时）
