---
layout: post
title: 密码管理-让生活更轻松高效
tags:
- 效率
categories: life
description: 用这个方法让你不再为记不住密码而烦恼
---

# 引言
from keepass

今天你需要记住许多密码。您需要Windows网络登录，您的电子邮件帐户，您网站的FTP密码，在线密码（如网站会员帐户）等等的密码等等。名单是无止境的。另外，您应该为每个帐户使用不同的密码。因为如果你在任何地方只使用一个密码，并且有人得到这个密码，你就会遇到问题...一个严重的问题 他将有权访问您的电子邮件帐户，网站等。难以想象。

相对于1Password的简洁高效，keepass的设置相当繁琐，但是作为一个崇尚开源的用户，对于不开源的东西总是不放心，所以，keepass是密码管理的绝佳选择。

# 我采用的密码管理思路

总的来说，本人采取了两种密码管理的策略，对于作为主要办公软件的Tim而言，每个月修改一次密码，这里的修改密码借鉴了知乎某人“用一个密码改变自己的生活”的观点，即结合月度目标，每次输入密码时都会激励促进月度目标的实现。

另一种即one password的思想，设置一个复杂的密码，用keepass软件来保存其他的所有密码，来解决记不住密码和密码安全性的问题！

# keepass使用方法

具体的教程就看官网把，基本上对于大多数的软件，官网的help文档都是最简单明了的

> keepass可以直接打开OneNote云文件，手机app即可实时更新keepass，两台不同的电脑，keepass文件保存在OneNote文件夹中，也可以实现实时更新。

# 在浏览器端共享密码保护

## 谷歌浏览器保存密码安全性有多高？

先说结论，安全性并不高，除非你设置一个很复杂的win10系统密码，而且不让别人知道你的系统密码。如果知道了你的系统密码，浏览器保存的密码就有被盗的可能性了！

**chrome的理由在于，系统密码就是你的主密码，也符合one password的想法，这个主密码需要好好保管，而不是让操作系统中的每个应用软件自己搞一套密码**

## 火狐密码基本不保护

被火狐浏览器吓到了！秘密居然都是明文存储！而且，想看的话都不需要输入任何密码，Chrome还需要输入主机密码呢！要么就是设置主密码，但是存密码读取密码都得输入主密码，还会莫名其妙的提示输入主密码。

一劳永逸解决方案：采用lastpass扩展，仅使用插件也是免费的。无奈客户端收费！但自己也用不到客户端，最重要的密码当然还是存在keepass本地了。

## ipad插件不能用

当然也就用不了pastpass了。但是你要明白自己的需求在哪里，ipad浏览器的作用仅仅在于查资料！根本就不需要登录哪个网站干什么！所以！没必要用任何插件！

# 补充：如何避免win10频繁的输入开机主密码

在登录选项里面设置PIN码或者图片密码即可，尤其是PIN码十分方面快捷，图片密码还有有点担心被别人看到，希望微软很快退出面部识别或者指纹解锁！

# 补充：密码分级管理

* 第一级：绝密，银行账号，支付宝账号
* 第二级：机密，社交网站账号，云网盘，云笔记
* 第三级：秘密，各种论坛，普通网站

显然，用了keepass，密码分级作用并不大！