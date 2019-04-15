---
layout: post
title: springBoot网站搭建笔记
tags:
- spring
categories: java
description: spring框架学习笔记
---

前端采用html、css、js等基础网页设计和velocity模板实现网页与后台数据的交互，本项目前端主要采用现成的代码，自己动手实现后台SpringBoot框架。

# SpringBoot 基本概念

* 控制反转IOC-Inversion of Control

不是什么技术，而是一种设计思想，一种重要的面向对象编程的法则，指导我们设计出松耦合、更优良的程序。将设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。[参考](https://www.cnblogs.com/xdp-gacl/p/4249939.html)

IoC对编程带来的最大改变不是从代码上，而是从思想上，发生了“主从换位”的变化。应用程序原本是老大，要获取什么资源都是主动出击，但是在IoC/DI思想中，应用程序就变成被动的了，被动的等待IoC容器来创建并注入它所需要的资源了。

谁控制谁，控制什么？传统的Java SE设计，我们直接在对象内部通过new进行创建对象，是程序主动的去创建依赖对象，而IOC是有专门的一个容器来创建这些对象，即由IoC容器来控制对象的创建；IoC容器控制对象；主要控制了外部资源的获取（不只是对象包括比如文件等）。

为何是反转，哪些地方反转了：有反转就有正转，传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，也就是正转；而反转则是由容器来帮忙创建及注入依赖对象；为何是反转？因为由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象，所以是反转；哪些地方反转了？依赖对象的获取被反转了。


---

* 依赖注入DI-Dependency Injection

组件之间的依赖关系在运行期决定，形象的说，即由容器动态的将某个依赖关系注入到组件之中。依赖注入的目的并非为软件系统带来更多功能，而是为了提升组件重用的频率，并为系统搭建一个灵活、可扩展的平台。通过依赖注入机制，我们只需要通过简单的配置，而无需任何代码就可指定目标需要的资源，完成自身的业务逻辑，而不需要关心具体的资源来自何处，由谁实现。

应用程序依赖于IoC容器，应用程序需要IoC容器来提供对象需要的外部资源；IoC容器注入应用程序的某个对象，应用程序依赖对象；注入了什么？注入某个对象所需要的外部资源（包括对象、资源、常量数据）。

**控制反转和依赖注入是同一个概念的不同角度描述，控制反转的概念比较含糊（可能只是理解为容器控制对象这一层面，很难让人想到谁来维护对象关系），依赖注入明确描述了被注入的对象依赖IoC容器配置依赖对象。**

所有的类都会在spring容器中登记，告诉spring你是个什么东西，你需要什么东西，然后spring会在系统运行到适当的时候，把你要的东西主动给你，同时也把你交给其他需要你的东西。所有的类的创建、销毁都由 spring来控制，也就是说控制对象生存周期的不再是引用它的对象，而是spring。对于某个具体的对象而言，以前是它控制其他对象，现在是所有对象都被spring控制，所以这叫控制反转。

---

* bean

在Spring中，那些组成你应用程序的主体及由Spring Ioc容器所管理的对象，都被称之为bean。简单来讲，bean就是Spring容器的初始化、配置及管理的对象。

---

* 注解

可以分为两类，首先是与bean容器相关的注解，或者说bean工厂相关的注解；先后有：@Required， @Autowired, @PostConstruct, @PreDestory，还有Spring3.0开始支持的JSR-330标准javax.inject.\*中的注解(@Inject, @Named, @Qualifier, @Provider, @Scope, @Singleton).

然后是springmvc相关的注解有：@Controller, @RequestMapping, @RequestParam， @ResponseBody等等。

---

* MVC-Model View Controller

模型指数据，就是 dao,bean；视图是网页, JSP，用来展示模型中的数据；控制器是什么？ 控制器的作用就是把不同的数据(Model)，显示在不同的视图(View)上，Servlet 扮演的就是这样的角色。

---

* AOP/Log

面向切面编程。处理所有流程都要处理的业务，比如Log。





# 创建spring boot项目

* 在https://start.spring.io开始项目，或者在IDEA中创建spring Assistant项目

项目位于'd://HelloWorld/demo',已在IDEA中上传至github

# 配置pom.xml文件，并添加依赖

[pom.xml详解](https://blog.csdn.net/u012152619/article/details/51485297)

Project Object Model，项目对象模型。通过xml格式保存的pom.xml文件。setting.xml主要用于配置maven的运行环境等一系列通用的属性，是全局级别的配置文件；而pom.xml主要描述了项目的maven坐标，依赖关系，开发者需要遵循的规则，缺陷管理系统，组织和licenses，以及其他所有的项目相关因素，是项目级别的配置文件。

* 修改spring版本为1.3.6以支持velocity
* 不同的springboot继承的junit不同，因此也要修改DemoApplicationTests中的代码。

[官方配置文档说明](https://docs.spring.io/spring-boot/docs/1.0.x-SNAPSHOT/reference/html/)

`application.properties`，这里修改velocity的文件后缀为html。

# 建立controller设置网站映射

* 设置测试'/hello'返回测试页面
* 配置`application.properties`文件,将返回velocity的后缀设置为html
* 测试通过url传递参数
* request和response代码测试
* 重定向，301永久跳转，302临时跳转
* 异常处理

# 测试velocity模板用法

主要包括变量传递、循环语句、字符串连接、宏、页面引用
[官网guide](https://blog.csdn.net/gaojinshan/article/details/23945879)

# 网站前端

* 在resources/templates下面建立`index.html`，用velocity语法写网站首页。

# 创建数据库

数据库采用驼峰命名方式 数据库中的head_url转化为对象中的headUrl

* application.properties增加spring配置数据库链接地址
* pom.xml引入mybatis-spring-boot-start和mysql-connect-java
* 复制配置文件mybatis-config.xml

按照视频内容配置的数据库，一直报错：`o.a.tomcat.jdbc.pool.ConnectionPool      : Unable to create initial connections of pool.`，初步确定是数据库连接的问题，数据库的配置参数不多，后来才发现，由于和视频中的数据库的版本不同，设置的url参数也是不同的，原视频中是`jdbc:mysql://localhost:3306/wenda?useUnicode=true&characterEncoding=utf8&useSSL=false`，缺少时区参数。改为：`jdbc:mysql://localhost:3306/wenda?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT`，程序即可正常运行。


# 进行数据库操作
第一种方法，用注解方式，本项目均采用此方式。

第二种方法，用xml方式。在相同的包目录下定义同名的XML。

# 创建service

dao层读写数据库，service层调用dao层，control层调用service。

xml可以做基本的逻辑操作。

# 加入toolbox配置


# 用户注册登录
`创建Model(User)`
注册的功能比较简单，无非就是update数据库即可，注意以下几点：

* 注意用户名合法性检测（长度、敏感词、重复、特殊字符）：如果用户名中含有html等，会把网页变乱。
* 密码长度要求、密码salt加密、密码强度检测md5。不用salt的话，简单密码的md5加密也是很容易破解的。MD5加密自己写md5工具函数即可。
* 用户邮件、短信激活。



登录：同样打开一个网站，登陆过的人看到的信息不同，这里采用了token。服务器端token关联userid，客户端存储token（app存储本地，浏览器存储cookie）。服务器客户端token有效期设置（记住登录）。token可以是sessionid，或者是cookie里的一个key。登出的时候删除token即可。`创建Model(LoginTicket)`

服务器与客户端一次有效的通话叫做session




注册：
UserDAO的添加用户已经写好了，在UserService中添加register接口。

设置变量LoginTicket保存用户登陆的信息。

用户注册或者登录成功后在cookie中加入ticket。

登录注册功能做好之后，继续做带token的http请求。

访问所有的页面时，都要对cookie进行验证。可以通过之前所学的切面编程的方法来做。
更好的是直接用springboot中的拦截器。

写完拦截器还需要配置一下

如果对接口的顺序不了解的话，请debug

同一个人的几次访问称为一个session。大的网站session是共享的，session有一个


未登录跳转：登陆完后再跳转回去。

# 问题发布
使用json之前添加fastjson依赖，定义jso小工具。

# 敏感词过滤：
敏感词组织成前缀树

# 多线程
前台发布之后就不管了，后台有线程专门来处理。

BlockQueue同步队列

threadLocal：
* 线程局部变量。即使是一个static成员，每个线程访问的变量也是不同的。
* 常见于web中存储当前用户到一个静态工具类中，在线程的任何地方都可以访问到当前线程的用户。
* 参考HostHolder中的user

Excutor
* 提供一个运行任务的框架
* 将任务和如何运行任务解耦
* 常用语提供线程池或定时人物服务

Future
* 返回异步结果
* 阻塞等待返回结果
* timeout
* 获取线程中的exception


评论中心：
统一的评论服务，覆盖所有的实体评论。不仅可以回答问题，站内信也是。

开发的六个步骤：
* 涉及到数据库中哪些字段
* Model：模型定义，与数据库想匹配
* DAO数据读取
* Service服务包装
* Controller业务入口
* Test
