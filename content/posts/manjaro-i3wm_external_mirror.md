---
title: "Manjaro I3wm接入外接显示器不显示"
date: 2019-05-29T11:59:08+08:00
draft: false
categories: ["linux"]
tags: ["manjaro","linux","i3wm"]
---

装了manjaro的i3wm桌面环境后，发现外界显示器无法显示。搜索资料后发现解决方法如下。

### 查看设备输入
终端输入:
```
xrandr
```
可以看到结果如下，其中 eDP1是我笔记本自带的屏幕，HDMI1就是我外接的显示器。
```
Screen 0: minimum 8 x 8, current 1920 x 1080, maximum 32767 x 32767
eDP1 connected primary 1920x1080+0+0 (normal left inverted right x axis y axis) 310mm x 170mm
   1920x1080     60.05*+  59.93  
   1680x1050     59.88  
   1400x1050     59.98  
   1600x900      60.00    59.95    59.82  
   1280x1024     60.02  
   1400x900      59.96    59.88  
   1280x960      60.00  
   1368x768      60.00    59.88    59.85  
   1280x800      59.81    59.91  
   1280x720      59.86    60.00    59.74  
   1024x768      60.00  
   1024x576      60.00    59.90    59.82  
   960x540       60.00    59.63    59.82  
   800x600       60.32    56.25  
   864x486       60.00    59.92    59.57  
   640x480       59.94  
   720x405       59.51    60.00    58.99  
   640x360       59.84    59.32    60.00  
DP1 disconnected (normal left inverted right x axis y axis)
HDMI1 connected (normal left inverted right x axis y axis)
   1920x1080     60.00 +  50.00    59.94  
   1920x1080i    60.00    50.00    59.94  
   1600x900      60.00  
   1280x1024     75.02    60.02  
   1152x864      75.00  
   1280x720      60.00    50.00    59.94  
   1024x768      75.03    60.00  
   800x600       75.00    60.32  
   720x576       50.00  
   720x576i      50.00  
   720x480       60.00    59.94  
   720x480i      60.00    59.94  
   640x480       75.00    60.00    59.94  
   720x400       70.08  
HDMI2 disconnected (normal left inverted right x axis y axis)
VIRTUAL1 disconnected (normal left inverted right x axis y axis)

```

### 双屏设置

- 设置主屏幕
```
 xrandr --auto --output eDP1 --primary
 ---auto：可以自动启用关闭的屏幕。 
 ---primary：设置主屏。
```
- 复制模式
```
 xrandr --auto --output eDP1 --pos 0x0 --mode 1920x1080 --output HDMI1 --same-as eDP1
 ---pos：起始位置，x。 
 ---same-as：与eDP1输出保持一致。
```
- 扩展模式
```
 xrandr --auto --output eDP1 --pos 0x0 --mode 1920x1080 --primary --output HDMI1 --mode 1024x768 --right-of eDP1
 --right-of：HDMI1的起始位置在eDP1的右边。
```
命令的结果就是HDM1 会在 eDP1 的右边，eDP1 为主屏，另外位置的参数还有 --left-of, --above, --below 等。
如果需要自定义两个屏幕的位置， 可以通过计算每个屏幕的分辨率， 用 --pos 参数来指定每个屏幕显示的位置。
- 单屏模式
```
 xrandr --output eDP1 --pos 0x0 --mode 1920x1080 --primary --output VGA1 --off
 --off:关闭某个屏幕.
```
- 自定义模式
```
另外屏幕的旋转, 镜像和缩放可以分别使用 --rotate, --reflect 和 --scale 参数来实现.
```
- 永久保存
```
如果需要永久保存配置，可以通过更改/etc/X11/xorg.conf或者/etc/X11/xorg.conf.d/****进行保存。
```
### 常用命令

- 外接显示器(--auto:最高分辨率)，与笔记本液晶屏幕显示同样内容（克隆）
```
xrandr --output HDMI1 --same-as eDP1 auto
```
- 打开外接显示器(分辨率为1920x1080)，与笔记本液晶屏幕显示同样内容（克隆）
```
 xrandr --output HDMI1 --same-as eDMP1 --mode 1920x1020
```
- 打开外接显示器(--auto:最高分辨率)，设置为右侧扩展屏幕
```
 xrandr --output HDMI1 --right-of eDP1 --auto
```
- 关闭外接显示器
```
 xrandr --output HDMI1 --off
```
- 打开外接显示器，同时关闭笔记本液晶屏幕（只用外接显示器工作）
```
 xrandr --output HDMI1 --auto --output eDP1 --off
```
- 关闭外接显示器，同时打开笔记本液晶屏幕 （只用笔记本液晶屏）
```
 xrandr --output HDMI1 --off --output eDP1 --auto
```

### 参考连接
[Arch Wiki]( https://wiki.archlinux.org/index.php/Xrandr_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29)

[使用Xrandr设置单屏和双屏](https://wiki.wh-redirect.deepin.cn/mediawiki/index.php?title=%E4%BD%BF%E7%94%A8Xrandr%E8%AE%BE%E7%BD%AE%E5%8D%95%E5%B1%8F%E5%92%8C%E5%8F%8C%E5%B1%8F)
