---
title: "Shadowsocks-libev+privoxy+gfwlist2privoxy"
date: 2019-05-28T12:01:14+08:00
draft: false
tags: ["linux","shadowsocks","vpn"]
categories: ["linux"]
---
### 本文所用到的工具
- [shadowsocks-libev](https://github.com/shadowsocks/shadowsocks-libev)
- [privoxy](https://www.privoxy.org/)
- [gfwlist2privoxy](https://github.com/zfl9/gfwlist2privoxy)

### shadowsocks-libev安装配置

下载安装:`sudo pacman -S shadowsocks-libev`

配置:

- 在`/etc/shadowsocks/`下新建`config.json`文件
- 编辑`config.json`：

```json
{
	"server":"xxxxxx",  #服务器IP
	"server_port":xxx, #服务器端口
	"local_address":"127.0.0.1", #本地监听IP
	"local_port":1080, #本地监听端口
	"password":"xxx", #密码
	"method":"xxx", #加密方式
	"fast_open":false, # tcp_fastopen
	"timeout":1000, #超时时间
	"workers":1 #workder进程数
}
```
- 启动ss-local: `ss-local -c /etc/shadowsocks/config.json`
- 测试:`curl -x socks5://127.0.0.1:1080 http://www.google.com`
- 设置守护进程启动:`systemctl start shadowsocks-libev@config`
** 注意：这里`@config`是指 `/etc/shadowsocks/`下面的配置文件 **
- 设置开机自启动: `systemctl enable shadows-libev@config`
### privoxy+gfwlist2privoxy安装配置
下载privoxy：`sudo pacman -S privoxy`

获取 gfwlist2privoxy 脚本:`curl -skL https://raw.github.com/zfl9/gfwlist2privoxy/master/gfwlist2privoxy -o gfwlist2privoxy`

生成 gfwlist.action 文件: `bash gfwlist2privoxy '127.0.0.1:1080' `

拷贝至 privoxy 配置目录:`cp -af gfwlist.action /etc/privoxy/
`

加载 gfwlist.action 文件 `echo 'actionsfile gfwlist.action' >> /etc/privoxy/config
`

启动privoxy:`sysemctl start privoxy`

设置开机自启动: `systemctl enable privoxy`

测试:
- 设置环境变量:
```shell
# 8118 为privoxy默认监听端口
export http_proxy = http:127.0.0.1:8118 
export https_proxy = http:127.0.0.1:8118 
```
- curl测试: `curl www.google.com `

### 设置环境变量
编辑 ` /etc/prifile `,加入以下两行:
```shell
export http_proxy = http:127.0.0.1:8118 
export https_proxy = http:127.0.0.1:8118 

```

重启，打开浏览器验证