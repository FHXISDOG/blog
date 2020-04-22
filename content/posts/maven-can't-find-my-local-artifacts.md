---
title: "Maven 本地有jar包仍拉取远程仓库的jar包"
date: 2020-04-22T12:00:26+08:00
draft: false
categories: ["maven"]
tags: ["maven","java"]
---

### 问题现象

使用Maven过程中，曾经出现过本地仓库中已经存在某jar包，但是Maven仍然从远程仓库下载jar包的现象，并且可能会报出类似以下的错误：

```shell
[exec] [ERROR]   The project com.acme:test:0.0.1-SNAPSHOT (/home/acme/pom.xml) has 1 error
[exec] [ERROR]     Non-resolvable parent POM: Failure to find com.acme.maven:parent-pom:pom:2 in http://repo.maven.apache.org/maven2 was cached in the local repository, resolution will not be reattempted until the update interval of central has elapsed or updates are forced and 'parent.relativePath' points at no local POM @ line 5, column 13 -> [Help 2]
```

## 解决方案

在stackoverflow看到了类似的问题，可以通过删除`_remote.repositories`文件解决问题。

Maven使用`_remote.repositories`文件存储本地jar对应的远程仓库源头。如果源头已经不存在该jar包（比如更改配置切换镜像、切换仓库导致的），Maven的resolve会失败，从而导致项目构建失败。删除了该文件以后，Maven不再从远程仓库执行此操作，因此可以解决问题。

## 参考资料

> [Maven偶现本地仓库jar存在仍然从远程仓库拉取且失败的现象 | Blog.new '书豪'](https://www.caosh.me/solution/maven-local-repository-does-not-work/)
> 
> [maven-cant-find-my-local-artifacts](https://stackoverflow.com/questions/16866978/maven-cant-find-my-local-artifacts)
> 
> [remote-repositories-prevents-maven-from-resolving-remote-parent](https://stackoverflow.com/questions/32571400/remote-repositories-prevents-maven-from-resolving-remote-parent)
