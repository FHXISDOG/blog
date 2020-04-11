---
title: "Manjaro安装displaylink"
date: 2019-06-24T13:06:10+08:00
draft: false
tags: ["manjaro","linux"]
topic: ["linux"]

---

---
使用华硕的便携笔记本需要安装displaylink驱动，官方又只提供windows驱动，在manjaro上使用需要自己安装驱动，经过研究，安装方法如下:
---

#### 准备工作
1. `uname -r`查看内核版本,输出:```4.19.23-1-MANJARO```,说明我的内核版本是419,执行`pacman -Ss linux-headers`查看库中的依赖，执行`sudo pacman -S core/liux419-headers`安装头文件
2. 安装evdi:`yay -S evdi-git`	3
3. 安装displaylink `yay -S displaylink`

#### 开始配置
1. 装载evdi: `sudo modprobe evdi`
2. 装载udl: `sudo modprobe udl`
3. 启用displaylink.sevice: `systemctl enable display link`
4. 开启displaylink.service:`systemctl start displaylink`
5. 新增配置文件

```
/usr/share/X11/xorg.conf.d/20-evdidevice.conf
---
Section "OutputClass"
	Identifier "DisplayLink"
	MatchDriver "evdi"
	Driver "modesetting"
	Option  "AccelMethod" "none"
EndSection
```
	 
6. 执行`xandr --listproviders`,可以看到输出:
```
Providers: number : 2
Provider 0: id: 0x45 cap: 0xb, Source Output, Sink Output, Sink Offload crtcs: 4 outputs: 2 associated providers: 1 name:Intel 
Provider 1: id: 0x14d cap: 0x2, Sink Output crtcs: 1 outputs: 1 associated providers: 1 name:modesetting
```
7. `lxrandr`开启屏幕
