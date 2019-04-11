---
layout: post
title: GitHub pages:jekyll模板安装调试以及遇到的问题
tags:
categories: tools
description: 本站搭建过程指南
---

# 1. 安装ruby.

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

# 2. gem换源

`gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/`

`gem sources -l`

# 3. 安装jekyll

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

# 5. 部署本地服务

进去到clone的username.github.io/ 目录下， 使用命令.$ `jekyll server --watch`
出现问题`Prepending `bundle exec` to your command may solve this`.时，此时只需执行” `bundle exec jekyll serve` “去启动服务器即可解决。

> jekyll server 如果你本机没配置过任何jekyll的环境，可能会报错.原因： 没有安装 bundler ，执行安装 bundler 命令$ gem install bundler.

[使用GitHub pages 搭建一个心仪的个人博客](https://blog.csdn.net/xudailong_blog/article/details/78762262)

[如何快速搭建自己的github.io博客](https://blog.csdn.net/walkerhau/article/details/77394659?utm_source=debugrun&utm_medium=referral)

# 模板常见问题

## 更改post文件后刷新浏览器，页面样式错乱

找到该文件`jekyll-jacman/_includes/_partial/head.html`,将 `<link rel="stylesheet" href="{{ site.baseurl }}{{ site.assets }}/css/style.css" type="text/css"> `中的`style.css`修改为`main.css`，同时修改对应位置的文件名称即可

## 可以在github pages内存储图片，但无法设置链接

应该用HTML格式的图片链接，在io库下建文件夹resource，然后将链接图片放入，采用链接格式为`<img src="{{ site.baseurl }}/resource/javaio.png">`,其中`{{ site.baseurl }}`是系统参数

## 如何在一个页面内设置到另一个页面的链接
