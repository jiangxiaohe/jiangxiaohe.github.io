---
layout: post
title: linux基本操作
tags:
categories: tools
description: ubuntu完全装机指南，装了多少次机，才知道总结走过的坑的重要性
---

[TOC]

# 服务器基本配置
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

* 配置ftp服务

1. 服务器安装vsftpd。`apt-get install`
2. 启动ftp服务`service vsftpd restart`
3. 查看vsftpd服务是否开启
4. 用户名密码和主机的用户名密码相同，主机创建的用户和密码均可以登录
3. 在阿里云控制台设置安全组
4. 配置文件`vim /etc/vsftpd.conf`

```
anonymous_enable=NO    //将YES修改为NO，禁止匿名登录
tcp_wrappers=YES
ascii_upload_enable=YES
ascii_download_enable=YES
write_enable=YES
```

* 查看系统信息

```
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

* `lscpu`查看cpu信息
	* CPU(s)显示了当前电脑的CPU为四核CPU
	* Model name显示处理器为Intel(R) Core(TM) i5-7400 CPU @ 3.00GHz
	* 也显示了L1、L2、L3内存大小

* `free -m`显示内存信息
	* men指物理内存，total列下对应物理内存大小为7892MB，即8G
	* used指已占用内存，available指目前可用内存大小
	* swap值分区代销

* 下载文件
	- `wget 路径`下载到当前文件夹

* 压缩和解压缩

* tar命令：
	* 解压：tar zxvf filename.tar
	* 打包：tar czvf filename.tar dirname
	* 对于.tar.gz文件同样用上述命令
	* 注意，在linux上用tar压缩文件后，在win系统上也必须用tar命令解压，用winrar不可以

* gz命令：
	* 解压1：gunzip FileName.gz
	* 解压2：gzip -d FileName.gz

* zip命令
	- 压缩：zip -r xxx.zip /home/user/fold
	- 解压：unzip -d /home/user/fold myfile.zip

* bz2命令
	- 压缩：bzip2 -d filename.bz
	- 解压：bunzip2 filename.bz

* 查看文件大小
du -h

* 查看磁盘挂载情况、查看磁盘使用率等
df -h
* 查看没有挂载的硬盘是否检测在系统中
lsblk
* 挂载
	* 创建一个要挂载的目录mkdir /data
	* 把格式化后的卷mount到目录/data：mount /dev/nvmeOn1 /data
	* 使用df -h检查是否正常
	* 到/etc/fstab下配置挂载信息，添加一条记录，如果有的话复制并修改即可
	* 添加完毕以后可以试一下fstab文件是否正常运行mount -a



* ufw防火墙配置

iptables可以灵活的定义防火墙规则， 功能非常强大。但是由此产生的副作用便是配置过于复杂。一向以简单易用著称Ubuntu在它的发行版中，附带了一个相对iptables简单很多的防火墙 配置工具：ufw。

```
ufw enable	开启防火墙
ufw allow 80 允许端口通过防火墙
ufw allow 22
ufw statues 查看防火墙状态
ufw disable 关闭防火墙
```

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

* 查看本机所有开启的服务
`service --status-all`查看本机所有开启的服务

`sudo service sshd stop|start|restart`

# 常用命令
* 下载文件wget+链接
* find命令

`find pathname -options [-print -exec -ok]`

* find /etc -name *s* 在目录里面搜索带有s的文件
* find / -amin -10在系统中搜索最后１０分钟访问的文件
* find / -atime -2查找在系统中最后４８小时访问的文件


* grep命令

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


* shell强制退出当前命令
ctrl+c

* 管道符与grep命令连用
`ls | grep 'ap'`搜索当前文档中含有ap字母的目录或者文档

`ls -l | grep total`即可查看文件总大小

`ls -l |grep "^-"|wc -l`统计某文件夹下文件的个数

# 文件操作


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

# 系统目录结构
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





# C、C++编译运行

* 用vim创建并保存hello.cpp文件
* 汇编。`gcc -S -m32 main.c`。.asm和.s:前者是dos和win下常见的源程序扩展名，后者是linux内核源程序中用的扩展名,本质上都是文本文档，没区别。
* 编译。`gcc -Wall hello.c -o hello`。该命令将文件‘hello.c’中的代码编译为机器码并存储在可执行文件 ‘hello’中。机器码的文件名是通过 -o 选项指定的。该选项通常作为命令行中的最后一个参数。如果被省略，输出文件默认为 ‘a.out’.-Wall 开启编译器几乎所有常用的警告。注意，编译cpp项目时用`g++`
* 执行。`./hello.out`

命令|解释
-|-
-E | preprocess only
-S | compile only, don't assemble or link
-c | compile and assemble, don't link
-o <file> | place the output into <file>

# gdb
https://blog.csdn.net/tzshlyt/article/details/53668885

用gdb调试程序前，必须使用gcc的-g或-ggdb选项编译程序
gcc -g -main.c -o main.o
-m32 生成32位机器的汇编代码；-m64则生成64位机器汇编代码；
-g 用于生成符号表用于gdb调试
gdb main.o 进入gdb调试环境
另外两种进入GDB的命令为：
* gdb -e executable    -c    corefile    -e命令后面跟指定的可执行文件
* gdb executable -pid process-id （使用命令 'ps -auxw' 可以查看进程的 pid）
l main 查看main函数
命令行参数|含义
-|:-:
--exec=file    -e file|指定可执行文件
--core=corefile    -c forefile|制定core文件
--command=file    -x core-file|从制定文件当中读取gdb命令
--diretory=directory    -d directory|把指定目录加入到源文件搜索路径
--cd=directory|以指定目录作为当前路径来运行GDB
--pid=process-id    -p process-id|指定要附属的进程ID


调试相关选项|含义
-|-
* 添加断点|
b 23 |在第23行添加断点
b add |在add函数入口添加断点
info break |查看断点信息
b * [地址] |在地址处设置断点，比如b * 0x7c00
* 删除断点|
delete 1 |删除1号断点
delete 1-3 |删除1-3号断点
clear 10 |删除第10行断点
clear main |删除函数的所有断点
* 运行程序|
r |运行程序
n |下一步（把函数当一条指令直接跳过）
s |下一步（会执行到函数内部）对于汇编代码，下一条指令用ni或si
c |运行到下一个断点处
finish |执行到函数结尾处
q |退出
* 查看运行过程中变量的值|
print/p |打印变量的值
display/undisplay sum|每次停止显示变量的值/取消跟踪，注意，display+变量名，undisplay+观察号|
