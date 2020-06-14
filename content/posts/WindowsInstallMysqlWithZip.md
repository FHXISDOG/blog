---
title: "windows以zip方式安装mysql"
date: 2020-06-14T14:43:27+08:00
draft: false
---

#### [官网](https://dev.mysql.com/downloads/)下载MySQL压缩包
#### 解压缩至目录D:/
#### 将`D:\mysql-8.0.11-winx64\bin`配置到环境变量`path`中
#### 在`D:\mysql-8.0.11-winx64`中新建`my.ini`文件，添加如下配置:

```
[mysqld]
# mysql位置
basedir=D:\mysql-8.0.11-winx64
# 数据保存位置
datadir=F:\MySql_Data
# 端口
port = 3306
```
#### 初始化数据库
```
mysqld --initialize --user=mysql --console
```
在控制台消息尾部会出现随机生成的初始密码，记下来（因为有特殊字符，很容易记错，最好把整个消息保存在记事本里）


#### 以管理员身份打开命令行,将mysql添加到服务
```
mysqld --install MySQL

net start MySQL

```
#### 启动Mysql并修改密码
```
 mysql -u root -p 
 #密码为刚刚初始化时生成的密码
```

#### 修改密码
```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass';
```

####  参考链接:

>[mysql官方链接](https://dev.mysql.com/doc/refman/8.0/en/windows-install-archive.html)
