---
layout: post
title: linux基本操作
tags:
categories: linux
description: ubuntu完全装机指南，装了多少次机，才知道总结走过的坑的重要性
---

[TOC]

# linux基本操作

## 后台命令nohup

nohup 是 `no hang up` 的缩写，就是不挂断的意思。

`nohup command > myout.file 2>&1 &`

在上面的例子中，0 – stdin (standard input)，1 – stdout (standard output)，2 – stderr (standard error).
2>&1是将标准错误（2）重定向到标准输出（&1），标准输出（&1）再被重定向输入到myout.file文件中。

用`jobs`命令可以查看后台运行的进程。

## sshpass和scp

随机复制文件
* head -n 10 显示前10行
* shuf 随机打乱
* xargs -I {} 该命令和管道符连用作为后面命令的指定参数。比如：ls | xargs -I {} scp {} nys@IP
* 上述命令采用scp需要每复制一个文件都需要输入ssh密码，可以用sshpass解决该问题。
* `ls| shuf | head -n 10000 | xargs -I {} sshpass -p "password" scp {} nys@IP`

## module load/unload
Envrionment modules工具用来快速的设置和修改用户编译运行环境。
Envrionment modules通过加载和卸载modulefile文件可直接改变用户的环境变量，用户不需要修改.bashrc，从而避免误操作。
`module load | add 加载环境变量`

* `module avail` -- 显示系统可用的编译器及库
* `module list`  显示用户加载的编译器及库
* `module help` 帮助命令

## find命令

`find pathname -options [-print -exec -ok]`

find /etc -name *s* 在目录里面搜索带有s的文件

find / -amin -10在系统中搜索最后１０分钟访问的文件

find / -atime -2查找在系统中最后４８小时访问的文件

find . -size +10M 查找当前文件夹大于10M的文件

find -type f 表示只查找文件，d表示查找目录

## ln链接命令
* `ln [参数][源文件或目录][目标文件或目录]`功能是为某一个文件在另外一个位置建立一个同步的链接。参数前要加-
	-b 删除，覆盖以前建立的链接
	-d 允许超级用户制作目录的硬链接
	-f 强制执行
	-i 交互模式，文件存在则提示用户是否覆盖
	-n 把符号链接视为一般目录
	-s 软链接(符号链接)
	-v 显示详细的处理过程

## grep命令

```
grep [-acinv] [--color=auto] '搜寻字符串' filename
选项与参数：
-a ：将 binary 文件以 text 文件的方式搜寻数据
-c ：计算找到 '搜寻字符串' 的次数
-i ：忽略大小写的不同，所以大小写视为相同
-n ：顺便输出行号
-v ：反向选择，亦即显示出没有 '搜寻字符串' 内容的那一行！
--color=auto ：可以将找到的关键词部分加上颜色的显示喔！

将/etc/passwd，有出现 root 的行取出来

# grep root /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
或
# cat /etc/passwd | grep root
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
```


* 管道符与grep命令连用
`ls | grep 'ap'`搜索当前文档中含有ap字母的目录或者文档

`ls -l | grep total`即可查看文件总大小

`ls -l |grep "^-"|wc -l`统计某文件夹下文件的个数

`ls | wc -l`显示文件个数

## 文件操作

|说明|操作|示例|
|-|-|-|
|新建文件夹|mkdir [选项] 目录| |
|列出目录|ls [选项] [目录名]|-l选项列出详细信息 -la查看隐藏文件 ll命令相当于ls -la|
|切换目录|cd [目录名]| 进入系统根目录 cd / 返回上一级目录cd.. 跳转到指定目录cd /echncms/b（根目录下进入）|
|删除|rm [选项] 文件|-rf * 删除当前目录下的所有文件,这个命令很危险，应避免使用。 -f 其中的，f参数 （f --force ） 忽略不存在的文件，不显示任何信息。不会提示确认信息。|
|新建文件|touch 文件||
|查看“当前工作目录”的完整路径| pwd [选项]| |
|移动文件或者目录|mv [选项] 源文件或目录 目标文件或目录| |
|复制文件或者目录|cp [选项]… [-T] 源 目的| |
|查看文件|cat [选项] [文件]| |
|编辑文件|cat [选项] [文件]| |

## 压缩和解压缩

* tar命令：
	* 解压：tar -zxvf filename.tar -C 指定目录
	* 打包：tar -czvf filename.tar dirname
	* 对于.tar.gz文件同样用上述命令
	* 注意，在linux上用tar压缩文件后，在win系统上也必须用tar命令解压，用winrar不可以
	* 有时候报错用命令`tar -xvf filename.tar`

* gz命令：
	* 解压1：gunzip FileName.gz
	* 解压2：gzip -d FileName.gz

* zip命令
	- 压缩：zip -r xxx.zip /home/user/fold
	- 解压：unzip -d /home/user/fold myfile.zip

* bz2命令
	- 压缩：bzip2 -d filename.bz
	- 解压：bunzip2 filename.bz

## 磁盘设置

lsblk
* 挂载
	* 创建一个要挂载的目录mkdir /data
	* 把格式化后的卷mount到目录/data：mount /dev/nvmeOn1 /data
	* 使用df -h检查是否正常
	* 到/etc/fstab下配置挂载信息，添加一条记录，如果有的话复制并修改即可
	* 添加完毕以后可以试一下fstab文件是否正常运行mount -a

* df -h 查看磁盘挂载情况、查看磁盘使用率等
* mount挂在局域网硬盘
  * 首先应配置nfs服务
  * 客户端安装`sudo apt install nfs-common`
  * `sudo mount -t nfs 192.168.1.204:/media/raid0 /home/user/mountdir`这里需要注意第一个目录是远程磁盘目录（如果是任意一个文件夹的话需要配置nfs），后一个是本地文件夹

* du -h 查看文件大小
* du -h -d 1只查看一级子目录

## 下载

`wget 路径`下载到当前文件夹

## 系统

* 查看系统信息

```shell
uname -a              # 查看内核/操作系统/CPU信息
cat /etc/issue        # 查看操作系统版本
cat /proc/version      #包含GCC的版本信息
cat /proc/cpuinf     # 查看CPU信息
hostname             # 查看计算机名
lspci -tv             # 列出所有PCI设备
lsusb -tv             # 列出所有USB设备
lsmod                 # 列出加载的内核模块
env                    # 查看环境变量
```

* 查看本机所有开启的服务
`service --status-all`查看本机所有开启的服务

`sudo service sshd stop|start|restart`

* `lscpu`查看cpu信息
	* CPU(s)显示了当前电脑的CPU为四核CPU
	* Model name显示处理器为Intel(R) Core(TM) i5-7400 CPU @ 3.00GHz
	* 也显示了L1、L2、L3内存大小

* `free -m`显示内存信息
	* men指物理内存，total列下对应物理内存大小为7892MB，即8G
	* used指已占用内存，available指目前可用内存大小
	* swap值分区代销

## 用户配置

* 查看用户信息

`cat /etc/passwd`

* 创建可以登录图形用户界面的用户

`sudo adduser nys`

然后根据系统提示进行密码和注释性描述的配置，全程不用自己输入其他命令即可配置成功，用户主目录和命令解析程序都是系统自动指定。

将该用户添加到sudo用户组：
* 方法1：修改 `/etc/sudoers` 文件；
* 方法2：`usermod -a -G sudo nys//注意改成你自己的用户名`
采用方法2，查看`/etc/group`文件，可以看到sudo行多了nys用户，`sudo:x:27:user1,user2,nys`，删除nys用户即可去除sudo权限


* 删除用户

`sudo userdel -r nys`包括删除相应文件夹

## 防火墙

* netstat命令查看本机端口连接情况

用于显示与IP、TCP、UDP和ICMP协议相关的统计数据，一般用于检验本机各端口的网络连接情况

```
t: tcp 查看tcp连接状态
u: udp 仅查看udp连接状态
l:--listening          display listening server sockets
p:--programs           display PID/Program name for sockets
e:--extend             display other/more information
n:--numeric            don't resolve names
netstat -tulpen 加上所有选项
```

状态信息
* `LISTEN`：侦听来自远方的TCP端口的连接请求.FTP服务启动后首先处于侦听（LISTENING）状态。
* `ESTABLISHED`：代表一个打开的连接.ESTABLISHED的意思是建立连接。表示两台机器正在通信。

* ufw防火墙配置

iptables可以灵活的定义防火墙规则， 功能非常强大。但是由此产生的副作用便是配置过于复杂。一向以简单易用著称Ubuntu在它的发行版中，附带了一个相对iptables简单很多的防火墙 配置工具：ufw。

```
ufw enable	开启防火墙
ufw allow 80 允许端口通过防火墙
ufw allow 22
ufw statues 查看防火墙状态
ufw disable 关闭防火墙
```


## 配置ftp服务

1. 服务器安装vsftpd。`apt-get install`
2. 启动ftp服务`service vsftpd restart`
3. 查看vsftpd服务是否开启
4. 用户名密码和主机的用户名密码相同，主机创建的用户和密码均可以登录
5. 在阿里云控制台设置安全组
6. 配置文件`vim /etc/vsftpd.conf`

```
anonymous_enable=NO    //将YES修改为NO，禁止匿名登录
tcp_wrappers=YES
ascii_upload_enable=YES
ascii_download_enable=YES
write_enable=YES
```


# 系统知识

## 系统目录结构
[reference](https://www.cnblogs.com/silence-hust/p/4319415.html)

了解Linux文件系统的目录结构，是学好Linux的至关重要的一步.，深入了解linux文件目录结构的标准和每个目录的详细功能，对于我们用好linux系统至关重要。

linux文件系统的最顶端是/，我们称/为Linux的root，也就是 Linux操作系统的文件系统。

|目录|含义|应放置内容|
|-|-|-|
|/bin|binary|系统有很多放置执行档的目录，但/bin比较特殊。因为/bin放置的是在单人维护模式下还能够被操作的指令。在/bin底下的指令可以被root与一般帐号所使用，主要有：cat,chmod(修改权限), chown, date, mv, mkdir, cp, bash等等常用的指令。|
|/boot|boot|主要放置开机会使用到的档案，包括Linux核心档案以及开机选单与开机所需设定档等等。Linux kernel常用的档名为：vmlinuz ，如果使用的是grub这个开机管理程式，则还会存在/boot/grub/这个目录。|
|/dev|设备device|在Linux系统上，任何装置与周边设备都是以档案的型态存在于这个目录当中。 只要通过存取这个目录下的某个档案，就等于存取某个装置。比要重要的档案有/dev/null, /dev/zero, /dev/tty , /dev/lp*, / dev/hd*, /dev/sd*等等|
|/etc|不是什么缩写，来源于法语，是and so on的意思|系统主要的设定档几乎都放置在这个目录内，例如人员的帐号密码档、各种服务的启始档等等。 一般来说，这个目录下的各档案属性是可以让一般使用者查阅的，但是只有root有权力修改。 FHS建议不要放置可执行档(binary)在这个目录中。 比较重要的档案有：/etc/inittab, /etc/init.d/, /etc/modprobe.conf, /etc/X11/, /etc/fstab, /etc/sysconfig/等等。 另外，其下重要的目录有：/etc/init.d/ ：所有服务的预设启动script都是放在这里的，例如要启动或者关闭iptables的话： /etc/init.d/iptables start、/etc/init.d/ iptables stop、/etc/xinetd.d/ ：这就是所谓的super daemon管理的各项服务的设定档目录。|
|/home|home|这是系统预设的使用者家目录(home directory)。 在你新增一个一般使用者帐号时，预设的使用者家目录都会规范到这里来。比较重要的是，家目录有两种代号：~ ：代表当前使用者的家目录，而 ~guest：则代表用户名为guest的家目录。|
|/lib|library|系统的函式库非常的多，而/lib放置的则是在开机时会用到的函式库，以及在/bin或/sbin底下的指令会呼叫的函式库而已 。 什么是函式库呢？妳可以将他想成是外挂，某些指令必须要有这些外挂才能够顺利完成程式的执行之意。 尤其重要的是/lib/modules/这个目录，因为该目录会放置核心相关的模组(驱动程式)。|
|/media|media|顾名思义，这个/media底下放置的就是可移除的装置。 包括软碟、光碟、DVD等等装置都暂时挂载于此。 常见的档名有：/media/floppy, /media/cdrom等等。|
|/mnt|mount的缩写，这里可直接理解为“挂载”|如果妳想要暂时挂载某些额外的装置，一般建议你可以放置到这个目录中。在古早时候，这个目录的用途与/media相同啦。 只是有了/media之后，这个目录就用来暂时挂载用了。|
|/opt|optional可选择目录|这个是给第三方协力软体放置的目录 。 什么是第三方协力软体啊？举例来说，KDE这个桌面管理系统是一个独立的计画，不过他可以安装到Linux系统中，因此KDE的软体就建议放置到此目录下了。 另外，如果妳想要自行安装额外的软体(非原本的distribution提供的)，那么也能够将你的软体安装到这里来。 不过，以前的Linux系统中，我们还是习惯放置在/usr/local目录下。|
|/root|root|系统管理员(root)的家目录。之所以放在这里，是因为如果进入单人维护模式而仅挂载根目录时，该目录就能够拥有root的家目录，所以我们会希望root的家目录与根目录放置在同一个分区中。|

初始化系统后进入/root目录，`cd ..`返回根目录。
