---
layout: post
title: python文件传输
tags:
categories: python
description: python版本的socket、ssh、sftp
---

# socket

实现图片传输和处理后返回的功能

* 共享代码`units.py`

```python
import struct
import numpy as np
import socket
import cv2

def cv_show(img):
    cv2.imshow('img',img)
    cv2.waitKey(0)
    cv2.destroyAllWindows()

def send_img(img,s):# img,socket
    h,w,c = img.shape
    buffer = struct.pack('LLL',h,w,c)
    s.sendall(buffer)
    buffer = struct.pack('{}B'.format(h*w*c),*(img.flatten()))
    filesize = h*w*c
    send_size = 0
    while send_size<filesize:
        if filesize - send_size > 1024:
            s.send(buffer[send_size:send_size+1024])
            send_size += 1024
        else:
            s.send(buffer[send_size:])
            send_size = filesize
    print('send success,len is',filesize)
def recv_img(conn):
    buffer = conn.recv(24)
    h,w,c = struct.unpack('LLL',buffer)
    filesize = h*w*c
    recvd_size = 0  # 定义已接收文件的大小
    img = b''
    while recvd_size < filesize:
        if filesize - recvd_size > 1024:
            data = conn.recv(1024)
            recvd_size += len(data)
        else:
            data = conn.recv(filesize - recvd_size)
            recvd_size = filesize
        img = img+data
    print('recv success,len is',len(img))
    img = struct.unpack('{}B'.format(h*w*c),img)
    img = np.array(img).astype(np.uint8).reshape(h,w,c)
    return img
```

* 服务器端EchoServer
```python
# -*- encoding: utf-8 -*-
import socket
import struct
import numpy as np
from units import recv_img,send_img
def process(img):
    img += 10
    return img
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
# host = '192.168.1.112' # 本机地址
host = ""
port = 40006
s.bind((host,port))
s.listen(5)
conn,addr = s.accept()
print('connet by:',addr)
img = ''
while True:
    buffer = conn.recv(24)
    if buffer:
        print(buffer)
        print(struct.unpack('5s',buffer))
        img = recv_img(conn)
        img = process(img)
        send_img(img,conn)
conn.close()
s.close()
```

* 客户端

```python
from utils import send_img,recv_img,cv_show
import socket
import cv2
import struct

IP = '192.168.1.98' #填写服务器端的IP地址
port = 40006 #端口号必须一致
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
try:
    s.connect((IP,port))
except Exception as e:
    print('server not find or not open')
    sys.exit()

h,w = 256,256
img = cv2.imread('lena.jpg')
img = cv2.resize(img,(h,w))

def process(img,s):
    hello = struct.pack('5s',b'hello')
    s.send(hello)
    send_img(img,s)
    return recv_img(s)

plt.imshow(img[...,::-1])
t = process(img,s)
plt.imshow(t[...,::-1])

s.close()
```

# paramiko库实现ssh和sftp

参考[一只小小寄居蟹](https://www.cnblogs.com/xiao-apple36/p/9144092.html)

ssh是一个协议，OpenSSH是其一个开源实现，paramiko库实现了SSHv2协议，利用这个包，我们可以在python代码中直接使用ssh协议对远程主机进行操作，而不需通过ssh命令。
安装`pip install paramiko`

paramiko包含两个核心组件：SSHClient和SFTPClient。
* SSHClient的作用类似于Linux的ssh命令，是对SSH会话的封装，该类封装了传输(Transport)，通道(Channel)及SFTPClient建立的方法(open_sftp)，通常用于执行远程命令。
* SFTPClient的作用类似与Linux的sftp命令，是对SFTP客户端的封装，用以实现远程文件操作，如文件上传、下载、修改文件权限等操作。

* ssh示例

```python
import paramiko
# 创建SSH对象
ssh = paramiko.SSHClient()
# 允许连接不在know_hosts文件中的主机
ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())#第一次登录的认证信息
# 连接服务器
ssh.connect(hostname='192.168.1.112', port=22, username='user', password='password')
# 执行命令
stdin, stdout, stderr = ssh.exec_command('ls')
# 获取命令结果
res,err = stdout.read(),stderr.read()
result = res if res else err
print(result.decode()) # 打印命令结果
# 关闭连接
ssh.close()
```

也可以使用ssh免密登录:

```python
import paramiko
# 配置私人密钥文件位置
private = paramiko.RSAKey.from_private_key_file('.ssh/id_rsa')
#实例化SSHClient
client = paramiko.SSHClient()
#自动添加策略，保存服务器的主机名和密钥信息，如果不添加，那么不再本地know_hosts文件中记录的主机将无法连接
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
#连接SSH服务端，以用户名和密码进行认证
client.connect(hostname='10.0.0.1',port=22,username='root',pkey=private)
```

* sftp举例

```python
import paramiko
transport = paramiko.Transport(('192.168.1.112', 22))
transport.connect(username='niyunsheng', password='niyunsheng')
sftp = paramiko.SFTPClient.from_transport(transport)

# 执行上传动作
localpath = "d://2.txt" # 注意这里win系统上的路径写法，不能用‘\’符号
remotepath = "/home/niyunsheng/2.txt" # linux系统上要写全路径
sftp.put(localpath, remotepath)
# 执行下载动作
localpath = "d://1.txt"
remotepath = "/home/niyunsheng/1.txt"
sftp.get(remotepath, localpath)
transport.close()
```
