---
layout: post
title: 阿里云ECS+WordPress搭建个人博客
tags:
categories: aliyun
description: 搭建个人博客的过程和中间遇到的问题
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

参考[配置过程](https://www.cnblogs.com/starof/p/4278370.html)解决这两个错误信息

最终解决：原来是阿里云组织了外网访问80端口，在阿里云控制台设置安全组规则即可。[参考](https://blog.csdn.net/qq_37608398/article/details/78163086)

可以看到apache的默认主页，位置再`/var/www/html/index.html`

# 配置ftp服务器
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

至此，可以看到FTP传输文件已经成功。

# 上传wordpress模板

第二天在学校连接`DIVI`网络重连FTP设置，显示错误`服务器发回了不可路由的地址。使用服务器地址代替`，网上上的方法都解决不了，折腾了一个小时重新思考，和昨晚传输成功相比，改变的变量只有网络，连接4G网络后上传wordpress至`/var/www/html/`成功。

* 将压缩包解压到该目录下`tar -xvf archive_name.tar.gz`
* 配置apache2指向这个路径，修改 `/etc/apache2/sites-available/`下的`000-default.conf`这个文件。然后改的部分只有 `DocumentRoot /var/www/html`改为`DocumentRoot /var/www/html/wordpress`即可
* 重启apache2服务器`service apache2 restart`
* mysql创建数据库

```
mysql -u name -p 之后再第二行输入密码
show databases; 注意输入分号，然后enter才会显示结果
create database wordpress; 创建数据库
```

然后wordpress提示`抱歉，我不能写入wp-config.php文件。您可以手工创建wp-config.php文件，并将以下文字粘贴于其中。`，在wordpress文件夹下创建，注意在文档结尾添加`?>`。

然后即可进入wordpress后台进行创作和网站装饰了！

# 文件运维

2019年2月1日，控制台报错：“wordpress IP验证不当漏洞”，内容为"wordpress /wp-includes/http.php文件中的wp_http_validate_url函数对输入IP验证不当，导致黑客可构造类似于012.10.10.10这样的畸形IP绕过验证，进行SSRF。"

解决：[参考](https://www.jiloc.com/44412.html)修改http.php文件其中两行即可。然后验证漏洞可发现已修复。
