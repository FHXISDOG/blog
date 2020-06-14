---
title: "docker时区问题"
date: 2020-06-14T15:15:29+08:00
draft: false
---

### 问题描述:

最近在使用dockers部署应用时发现,每次执行更新时时间会往后推8个小时，
查看数据库间连接:`db.url=jdbc:mysql://xxxxx/xx?useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT%2b8&autoReconnect=true&failOverReadOnly=false`

使用`java`的`LocalDateTime.now()`打印出的时间也是快了8个小时


### 解决方案:

`docker`创建应用的容器时，不仅要将`/etc/localtime`挂载进去，也要将时区挂载进去.

例如:

```shell
 docker run --name app-name -d \
 -v /etc/localtime:/etc/localtime \
 -v /app:/app
 --network xx-bridge \
 -p 8080:8080 java:8 \
 sh -c 'echo "Asia/Shanghai" > /etc/timezone && java -jar /app/app.jar '
```
