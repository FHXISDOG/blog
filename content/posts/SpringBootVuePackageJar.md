---
title: "将vue打包到springboot静态资源中"
date: 2019-09-04T16:48:24+08:00
draft: false
categories: ["spring boot"]
tags: ["vue","java","spring boot"]
---

项目中遇到需要将vue打包到spring boot 中的static中，直接访问，不需要使用Nginx来访问nginx.具体做法如下:

### 将vue工程放到spring boot工程最外层

### 修改pom文件

```
      <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <executions>

                    <!--程序打包的时候，利用本地已安装的node环境，同时编译管理后台前端的工程文件-->
                    <execution>
                        <id>npm-build-background</id>
                        <phase>verify</phase>
                        <goals>
                            <goal>exec</goal>
                        </goals>
                        <configuration>
                            <executable>${npm.command}</executable>
                            <arguments>
                                <argument>run</argument>
                                <argument>prod-package</argument>
                            </arguments>
                            <workingDirectory>${basedir}/consumable-background</workingDirectory>
                        </configuration>
                    </execution>
                </executions>
        </plugin>

        <plugin>
                <artifactId>maven-resources-plugin</artifactId>
                <version>3.1.0</version>
                <executions>
                    <!--添加管理后台前端工程的静态文件到jar包中-->
                    <execution>
                        <id>copy-consumable-background</id>
                        <phase>prepare-package</phase>
                        <goals>
                            <goal>copy-resources</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>${basedir}/target/classes/static/background</outputDirectory>
                            <resources>
                                <resource>
                                    <directory>${basedir}/consumable-background/dist</directory>
                                </resource>
                            </resources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
```

### 配置拦截器(此步可以省略,应为springboot static 路径是默认放开的)

```
    @Override
    public void addResourceHandlers(org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**")
                .addResourceLocations("classpath:/static/");
    }            
```
