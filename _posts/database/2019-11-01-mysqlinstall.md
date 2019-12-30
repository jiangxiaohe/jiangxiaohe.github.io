---
layout: post
title: 数据库系统搭建
tags:
categories: database
description: 在服务器搭建数据库并且实现远程连接
---

# 搭建mysql数据库

* 切换root
* 更新源`sudo apt-get update`
* 安装：`apt-get install mysql-server`
* 设置：`service mysql start/stop/restart`
* 查看进程状态：`service mysql status`
* 查看mysql进程:`ps -ef | grep mysql`
* 查看mysql监听的端口:`netstat -lnp | grep mysql`
* 修改配置文件：`vim /etc/mysql/my.cnf`

mysql默认运行在3306端口，可以查看配置文件得到。

输入`sudo service mysql start`报错`start: Job failed to start`，这时候可采取[如下做法](https://stackoverflow.com/questions/22909060/mysql-job-failed-to-start)：
* 出现这个错误的原因是第二次安装时，上次安装没有卸载完全（有残余），故需要重新安装。
* 先备份库文件夹
  * `sudo mkdir /home/<your username>/mysql/`
  * `cd /var/lib/mysql/`
  * `sudo cp * /home/<your username>/mysql/ -R`
* 完全清楚mysql`apt-get remove mysql-*`、`dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P`
* 重新安装mysql

* 修改mysql密码
  * 停止mysql
  * 进入配置文件`vim /etc/my.cnf`
  * 在[mysqld]后添加`skip-grant-tables`（登录时跳过权限检查）
  * 进入mysql，`mysql -u root -p`，直接回车即可
  * 修改密码`set password for 'root'@'localhost'=password('NYSnys');`
  * 如果报错`ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement`,请先输入`flush privileges;`

# 进入数据库操作

[21分钟 MySQL 入门教程](https://www.cnblogs.com/mr-wid/archive/2013/05/09/3068229.html#d2)

* 登录数据库`mysql -h 主机名 -u 用户名 -p`
* 查看端口号`show global variables like 'port';`
* 查看所有的数据库`show databases;`
* 进入示例数据库`use informance_schema;`，修改成功后提示`Database changed`
* 查看该数据库中有哪些表`show tables;`
* 查看表基本信息`desc TABLES;`

# 设置远程访问数据库

1. 服务器端授权访问数据库：

例如，你想root使用123456从任何主机连接到mysql服务器。

`mysql>GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;`

如果你想允许用户jack从ip为10.10.50.127的主机连接到mysql服务器，并使用654321作为密码

`mysql>GRANT ALL PRIVILEGES ON *.* TO 'jack'@’10.10.50.127’ IDENTIFIED BY '654321' WITH GRANT OPTION;`

使修改生效`mysql>flush privileges;`

2. 修改`vim /etc/my.cnf`配置文件

将`bind-address = 127.0.0.1`注释掉

3. 重启服务
4. 本地命令行连接：

```
mysql -u 用户名 -p密码 -h 服务器IP地址 -P 服务器端MySQL端口号 -D 数据库名
比如：mysql -u root -h 192.168.1.88 -P 3306 -D mydb -p
也可以这样：mysql -u root -h 47.93.52.194 -p
即默认端口不用连接
```

5. python3连接sql服务器

`conda install PyMySQL`

```python
import pymysql
#打开数据库连接
db = pymysql.connect("192.168.122.58","eric","eric2017","test")
#pymysql.connect("localhost/IP","username","pwd","databs" )
#使用cursor()方法创建一个游标对象 cursor
cursor = db.cursor()
#使用execute()方法执行sql查询
cursor.execute("select VERSION()")
#使用 fetchone()方法获取单条数据
data = cursor.fetchone()
print ("database version : %s" % data)
#关闭连接
db.close()
```