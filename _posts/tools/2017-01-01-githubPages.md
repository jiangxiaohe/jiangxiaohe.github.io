---
layout: post
title: GitHub pages
tags:
categories: tools
description: 本站搭建过程指南.jekyll模板安装调试以及遇到的问题
---

# 创建GitHub pages:jekyll模版

找到一个模版，然后fork到自己的github，修改名字为`name.github.io`即可。


# 阿里云域名解析


选择[云解析dns]，可以看到审核通过的域名xxx.com，选择解析。增加下面两条数据：

方法一：cname方式

```
记录类型    主机记录    解析线路(运营商)   记录值 MX优先级   TTL 状态  操作
CNAME   @   默认  xx.github.io    --  10分钟        修改|暂停|删除|备注
CNAME   www 默认  xx.github.io    --  10分钟        修改|暂停|删除|备注
```

方法二:ip地址方式
在终端执行ping xx.github.io指令，获取ip地址a.b.c.d

```
记录类型    主机记录    解析线路(运营商)   记录值 MX优先级   TTL 状态  操作
A   @   默认  a.b.c.d     --  10分钟        修改|暂停|删除|备注
A   www 默认  a.b.c.d     --  10分钟        修改|暂停|删除|备注
```

等一段时间执行，便可在浏览器执行`www.niyunsheng.top`和`niyunsheng.top`进行访问了。

在cname文件里设置域名为`niyunsheng.top`

这是，网站的访问域名就是`https://niyunsheng.top`

> Chrome会强制将http重定向到https，就算是在浏览器手动输入http://xxx也不可以。
解决方案：
1. 在chrome的地址栏输入chrome://net-internals/#hsts，在Query HSTS/PKP domain中查询指定的域是否有HSTS记录，如果存在，在Delete domain security policies中删除该域即可。
2. 如果完成第一步后仍不能访问，可以尝试清除浏览器缓存。


# 设置全站cdn加速

* cdn

Content Delivery Network或Content Ddistribute Network，即内容分发网络.尽可能避开互联网上有可能影响数据传输速度和稳定性的瓶颈和环节，使内容传输的更快、更稳定。通过在网络各处放置节点服务器所构成的在现有的互联网基础之上的一层智能虚拟网络，CDN系统能够实时地根据网络流量和各节点的连接、负载状况以及到用户的距离和响应时间等综合信息将用户的请求重新导向离用户最近的服务节点上。


CDN(Content Delivery Network)内容发布网络，基本思路就是将你网站放置在各地节点服务器，用户访问时找最近的节点服务器获取数据，达到加速的目的。

腾讯云上创建一个CDN服务
虽然七牛融合云CDN对于http流量每个月都有10GB的免费流量，但是网站默认是https，而且本网站流量不多，也不需要费流量。



# 配置七牛云图床


# 模版常见问题

## 更改post文件后刷新浏览器，页面样式错乱

找到该文件`jekyll-jacman/_includes/_partial/head.html`,将 `<link rel="stylesheet" href="{{ site.baseurl }}{{ site.assets }}/css/style.css" type="text/css"> `中的`style.css`修改为`main.css`，同时修改对应位置的文件名称即可

## 可以在github pages内存储图片，但无法设置链接

应该用HTML格式的图片链接，在io库下建文件夹resource，然后将链接图片放入，采用链接格式为`<img src="{{ site.baseurl }}/resource/javaio.png">`,其中`{{ site.baseurl }}`是系统参数

## 如何在一个页面内设置到另一个页面的链接

<img src="{{ site.baseurl }}/resource/decorator.jpg">

# 在本地部署jekyll编译服务器
1. 安装ruby.

确保已安装`Ruby 2.1.0` 或更高版本：

```sh
ruby --version
```

安装`Bundler`：

```sh
gem install bundler
```

win系统下一定要选择ruby with devkit。
需要有下载Ruby环境，选最新的那个版本即可，官网上面安装列中有一个RubyGems，但Ruby1.9.1 以后版本已经自带了，所以无需额外下载

> 注意，安装过程中不需要选msys2.可能你并不太了解msys2，但是作为一个程序员，你一定知道mingw，而msys2就集成了mingw，同时msys2还有一些其他的特性，例如包管理器等。
>
> msys2可以在windows下搭建一个完美的类linux环境，包括bash、vim、gcc、make等工具都可以通过包管理器来添加和卸载
>
> msys2的包管理器是使用的pacman，用过archlinux的应该都知道pacman了。
>
> 我们现在的目标是要集成make+gcc+gdb的一条编译工具。
[windows搭建gcc开发环境(msys2)](https://blog.csdn.net/qiuzhiqian1990/article/details/56671839)

2. gem换源

`gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/`

`gem sources -l`

3. 安装jekyll

`gem install jekyll`

在 Github 上建一个库，库的名字是名为 xxx.github.com，其中的xxx是你的github的账号名

在Jekyll模板中选择自己喜欢的版面clone到本地

对模板中的信息进行修改，将模板push到自己的库中

下载 Jacman 主题：
```sh
git clone https://github.com/Simpleyyt/jekyll-jacman.git
cd jekyll-jacman
```

安装依赖：`bundle install`

运行Jekyll：`bundle exec jekyll server`

4. 部署本地服务

进去到clone的username.github.io/ 目录下， 使用命令.$ `jekyll server --watch`
出现问题`Prepending `bundle exec` to your command may solve this`.时，此时只需执行” `bundle exec jekyll serve` “去启动服务器即可解决。

> jekyll server 如果你本机没配置过任何jekyll的环境，可能会报错.原因： 没有安装 bundler ，执行安装 bundler 命令$ gem install bundler.

[使用GitHub pages 搭建一个心仪的个人博客](https://blog.csdn.net/xudailong_blog/article/details/78762262)

[如何快速搭建自己的github.io博客](https://blog.csdn.net/walkerhau/article/details/77394659?utm_source=debugrun&utm_medium=referral)


