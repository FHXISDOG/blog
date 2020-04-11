---
title: "win10自带输入法添加小鹤双拼"
date: 2020-04-11T16:53:23+08:00
draft: false
categories: ["tips"]
tags: ["tips"]
---

今天发现 Win10 自带的微软输入法还可以添加 小鹤双拼，终于可以抛弃其他的广告输入法了。

步骤：

1. win + R，输入 regedit，打开注册表

2. 找到  ***计算机\HKEY_CURRENT_USER\Software\Microsoft\InputMethod\Settings\CHS*** 项

3. 新建一个名为 ***UserDefinedDoublePinyinScheme0*** 的字符串值，值为 ***小鹤双拼*2*^*iuvdjhcwfg^xmlnpbksqszxkrltvyovt***

4. 打开控制面板--微软拼音输入法设置，把 小鹤双拼 设置为双拼的默认选择即可。

其他高级配置：

控制面板--时间和语言--语言--拼写，输入和键盘设置--高级键盘设置--允许我为每个应用窗口使用不同的输入法 -- 打勾

> 参考连接:[Win10 微软拼音添加小鹤双拼以及其他配置](https://ifttl.com/add-flypy-to-win10-microsoft-pinyin-and-other-configuration)
