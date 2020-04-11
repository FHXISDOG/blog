---
title: "多模块Maven工程单独打包某一模块工程"
date: 2019-11-12T13:28:47+08:00
draft: false
categories: ["spring boot"]
tags: ["maven","java","spring boot"]
---

### 问题描述

如果项目是多模块，如果只想打包某一模块，会出现找不到依赖的错误提示。

项目结构如下:

```
A:
----B
----C
----D
```

如果想只打包C模块，可以执行以下命令:

`mvn -pl -am  C package -DskipTests -Ptest`

多模块工程的打包命令参考：

```
-am --also-make 同时构建所列模块的依赖模块；
-amd -also-make-dependents 同时构建依赖于所列模块的模块；
-pl --projects <arg> 构建制定的模块，模块间用逗号分隔；
-rf -resume-from <arg> 从指定的模块恢复反应堆。
```

看英文的更助于理解：

```
-am,--also-make
    If project list is specified, also build projects required by the list

-amd,--also-make-dependents
    If project list is specified, also build projects that depend on projects on the list

-pl,--projects <arg>
    Comma-delimited list of specified reactor projects to build instead of all projects. A project can be specified by [groupId]:artifactId or by its relative path.

-rf,--resume-from <arg>
    Resume reactor from specified project
```

### 参考:

> [https://my.oschina.net/ccor/blog/704365](https://my.oschina.net/ccor/blog/704365)

@
