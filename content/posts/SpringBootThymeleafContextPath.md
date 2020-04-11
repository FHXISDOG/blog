---
title: "Spring Boot Thymeleaf 引用contexpath"
date: 2019-09-04T16:03:04+08:00
draft: false
categories: ["spring boot"]
tags: ["java","spring boot"]
---

spring boot 设置contextPath后，如果js中的请求路径不设置，会造成404的问题。
设置方法:

在html页面header中添加配置:

```
<meta name="ctx" th:content="${#httpServletRequest.getContextPath()}" >
```

js中引用:

```
 var ctx = $("meta[name='ctx']").attr("content");
```

变量ctx就是contextPath