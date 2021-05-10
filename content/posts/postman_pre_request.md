---
title: "Postman 在请求前添加自动执行脚本"
date: 2021-05-10T16:09:30+08:00
draft: false
categories: ["postman"]
tags: ["postman","skill"]
---



平时使用postman时经常会有需要在一下请求前先执行一些脚本，或者先统一发一次请求的需求，例如：先登陆再请求其他接口。经过摸索，发现postman可以在项目前统一执行脚本。步骤如下(以执行登陆为例):

1.新建一个`Collections`,点这个`Collections`,在右侧可以看到`Pre-request Script`选项。在里面填写自己要执行的脚本即可。

2. `Example`：

   ```JavaScript
   pm.sendRequest(
     {
       method: "POST",
       url: `${pm.environment.get("protocal")}://${pm.environment.get("host")}:${pm.environment.get("port")}${pm.environment.get("base-path")}/authentication/login-name`,
       header: { 
          'Content-Type': 'application/json'
       },
       body: {
           mode: "raw",
           raw: JSON.stringify({
               loginName: pm.environment.get('loginName'), //从环境变量中获取登录名和密码
               password: pm.environment.get('password')
           })
       },
     },
     function (err, response) {
       pm.environment.set("Authorization", response.json().data);
     }
   );
   //添加请求头
   pm.request.headers.add({ 
       // These keys appears when you set a header by hand. Just for fun they are here
       disabled: false,
       description:{
           content: "DescriptionTest",
           type: "text/plain"
       },
       // Your header, effectively
       key: 'KeyTest', 
       name: 'NameTest', 
       // If you set a variable you can access it
       // HeaderTest here has value="ValueHeaderTest"
       value: pm.collectionVariables.get("HeaderTest")
   }); 
   ```

   