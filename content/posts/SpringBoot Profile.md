---
title: "Spring Boot 分环境打包"
date: 2019-09-04T16:33:58+08:00
draft: false
categories: ["spring boot"]
tags: ["java","spring boot"]
---


使用spring boot工程时，虽然官方提供了application.profiles.active参数去激活不同环境，但是如果打包时忘了修改还是比较麻烦的时。可以使用maven插件，自动的更新不同环境的
的配置文件。

配置方法如下:

这里列举三个环境:dev,test,prod

resource目录结构如下(custom-config.properties为自定义的配置文件,区分不同环境也是以dev,test,prod结尾,如:custom-config-dev.properties):
 
    --- resource 
        --- application.yml
        --- application-dev.yml
        --- application-test.yml
        --- application-prod.yml
        --- custom-config.properties
        --- custom-config-dev.properties
        --- custom-config-test.properties
        --- custom-config-prod.properties
        --- application-common.yml


### 修改pom文件:

```
    <profiles>
        <profile>
            <id>dev</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <profile.active>dev</profile.active>
            </properties>
        </profile>
        <profile>
            <id>test</id>
            <properties>
                <profile.active>test</profile.active>
            </properties>
        </profile>
        <profile>
            <id>prod</id>
            <properties>
                <profile.active>prod</profile.active>
            </properties>
        </profile>

    </profiles>

    <build>
        <finalName>app</finalName>
        <plugins>

        </plugins>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <excludes>
                    <exclude>application-*.yml</exclude>
                    <exclude>custom-config.properties</exclude>
                </excludes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <!-- 是否替换@xx@表示的maven properties属性值 -->
                <filtering>true</filtering>
                <includes>
                    <include>application.yml</include>
                    <include>application-${profile.active}.yml</include>
                    <include>application-common.yml</include>
                    <include>custom-config.properties</include>
                </includes>
            </resource>
        </resources>
        <filters>
            <filter>
                src/main/resources/custom-config-${profile.active}.properties
            </filter>
        </filters>
    </build>
```

### 修改配置文件:

application.ym文件修改:
```
spring:
  profiles:
    active: @profile.active@
    include: common
```

这里 @xxx@相当于占位符，打包时会自动替换成dev,test,prod


custom-config.propertis文件修改如下:
```
customA=@customA@
customB=@customB@

```

custom-config-dev.properties文件:

```
customA=dev-customA
customB=dev-customB
```

这样就可以替换自定义的配置文件