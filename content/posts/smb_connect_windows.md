---
title: "Smb无法连接windows共享文件夹"
date: 2019-05-28T13:05:29+08:00
draft: false
categories: ["linux"]
tags: ["smb","linux"]
---

### 问题描述

使用`pacmanfm`在地址栏输入:`smb://fhxisdog-win/temp`访问windows共享文件夹，提示`NO FILE OR DIRECTIONARY`

在命令行使用命令:`smbclient -L fhxisdog-win -U%` 提示:`NT_STATUS_ACCESS_DENIED`

使用:` smbclient -L fhxisdog-win -m SMB2 `可以访问


### 解决方法

编辑 `/etc/samba/smb.conf`:在`[gloabl]`下加入:
```
min protocOl = SMB2
max protocol = SMB2
client min protocol = SMB2
client max protocol = SMB2
```
### smb简介
>服务器消息块（Server Message Block，缩写为SMB），又称网络文件共享系统（Common Internet File System，缩写为CIFS, /ˈsɪfs/），一种应用层网络传输协议，由微软开发，主要功能是使网络上的机器能够共享计算机文件、打印机、串行端口和通讯等资源。它也提供经认证的行程间通信机能。它主要用在装有Microsoft Windows的机器上，在这样的机器上被称为Microsoft Windows Network。

>经过Unix服务器厂商重新开发后，它可以用于连接Unix服务器和Windows客户机，执行打印和文件共享等任务。

>与功能类似的网络文件系统（NFS）相比，NFS的消息格式是固定长度，而CIFS的消息格式大多数是可变长度，这增加了协议的复杂性。CIFS消息一般使用NetBIOS或TCP协议发送，分别使用不同的端口139或445，当前倾向于使用445端口。CIFS的消息包括一个信头（32字节）和消息体（1个或多个，可变长）。


参考连接:[wiki](https://zh.wikipedia.org/zh-cn/%E4%BC%BA%E6%9C%8D%E5%99%A8%E8%A8%8A%E6%81%AF%E5%8D%80%E5%A1%8A)
