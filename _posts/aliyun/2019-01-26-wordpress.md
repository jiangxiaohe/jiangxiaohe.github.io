---
layout: post
title: 阿里云ECS+WordPress搭建个人博客
tags:
categories: aliyun
description:
---

# 基础知识回顾
http（80） mysql（3306） ssh（22）

# 安装lamp环境
```
sudo apt-get install apache2
sudo apt-get install mysql-server
sudo apt-get install phpmyadmin
```

出现问题：在本机上打开远程主机IP，但是没有出现Apache的启动页面。

* ufw查看防火墙配置。这里不是防火墙的问题，因为根本就没开启防火墙。如果开启的话，关闭即可。
* 测试远程主机端口是否开启`telnet 47.93.52.194 80`。发现连接不上。
* `service --status-all`查看本机所有开启的服务，辅助grep可以看到Apache2辅助grep可以看到Apache是开启的
* `netstat -tulpen`可以看到Apache2是开启的

最后锁定Apache2配置的问题，注意这里是Apache2而不是Apache，安装的目录应该是`/etc/apache2/apache2.conf`，而非`/etc/httpd/conf/httpd.conf`,用find命令可以快速找到，之所以找了这么久关键是搞错了文件名。其实当初用'find \ -name \*.conf | grep apache'时已经发现了这个文件，只不过文件太多没有留意。

重启apache服务器`service apache2 restart`，可以看到错误信息
* `AH00557: apache2: apr_sockaddr_info_get() failed for iZ2zebcoz7l31799mqih8nZ`
解决办法：在/etc/hosts中增加对web01的解析。然后重启apache不再报错AH00557。
`# cat /etc/hosts`                   
`127.0.0.1       localhost iZ2zebcoz7l31799mqih8nZ`
* `AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.0.1. Set the 'ServerName' directive globally to suppress this message`
解决办法：在apache配置文件apache2.conf中添加`ServerName localhost`，添加到随便一行即可。

参考`配置过程`(https://www.cnblogs.com/starof/p/4278370.html)解决这两个错误信息

最终解决：原来是阿里云组织了外网访问80端口，在阿里云控制台设置安全组规则即可。[参考](https://blog.csdn.net/qq_37608398/article/details/78163086)

可以看到apache的默认主页，位置再`/var/www/html/index.html`

# 配置ftp服务器上传wordpress模板
有了配置apache的经验，配置ftp服务后，记得在阿里云安全组中增加ftp访问端口。[参考](https://www.jianshu.com/p/f1d011d2d50b)


1. 服务器安装vsftpd。`apt-get install`
2. 启动ftp服务`service vsftpd restart`
3. 在阿里云控制台设置安全组
4. 配置文件`vim /etc/vsftpd.conf`

```
anonymous_enable=NO    //将YES修改为NO，禁止匿名登录
tcp_wrappers=YES
ascii_upload_enable=YES
ascii_download_enable=YES
write_enable=YES
```
打开同目录下的 ftpusers，把 root删掉