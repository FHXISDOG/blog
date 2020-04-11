---
title: "Linux使用modmap替换super和CapsLock位置"
date: 2019-05-28T15:50:39+08:00
draft: false
categories: ["linux"]
tags: ["modmap","linux","i3wm"]
---

manjaro的i3wm版本默认的mod键是super(就是windows键)，为了拯救我的小拇指，所以使用xmodmap将`super`键和`CapsLock`互换位置。

---

### 创建自己的映射表
```
xmodmap -pke > ~/.Xmodmap
```

### 创建映射

编辑```～/.Xmodmap```,在底部加入:
```
remove Lock = Caps_Lock
remove Mod4 = Super_L
keysym Super_L = Caps_Lock
keysym Caps_Lock = Super_L
add Lock = Caps_Lock
add Mod4 = Super_L
```

### 测试:
```
xmodmap ~/.Xmodmap
```
### 设置开机启动:

默认系统读取的就是$HOME/.Xmodmap

### 参考连接
[arch-wiki](https://wiki.archlinux.org/index.php/Xmodmap_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29)

[https://www.linuxidc.com/Linux/2013-05/84542.htm](https://www.linuxidc.com/Linux/2013-05/84542.htm)

