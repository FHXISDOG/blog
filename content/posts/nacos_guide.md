---
title: "nacos使用指南"
date: 2019-05-29T15:33:57+08:00
draft: false
categories: ["nacos"]
tags: ["java","nacos","spring cloud"]
---
# 服务启动
## 本地单机启动
### 下载源码
#### 从github上下载源码方式:
```
git clone https://github.com/alibaba/nacos.git
cd nacos/
mvn -Prelease-nacos clean install -U  
ls -al distribution/target/

// change the $version to your actual path
cd distribution/target/nacos-server-$version/nacos/bin
```
#### 下载编译后压缩包方式
从 [最新稳定版本](https://github.com/alibaba/nacos/releases) 下载 nacos-server-$version.zip 包
```
 unzip nacos-server-$version.zip 或者 tar -xvf nacos-server-$version.tar.gz
 cd nacos/bin
```
### 启动服务
#### Linux/Unix/Mac
```
sh startup.sh -m standalone
```
#### windows
```
cmd startup.cmd

```
或者双击startup.cmd运行文件。
### 测试
浏览器访问:```localhost:8848/nacos```(默认用户名/密码:nacos/nacos)
## 单机无数据库docker启动
### 下载镜像
```
docker pull nacos/nacos-server
```
### 启动docker
```
docker run -d --name nacos -e PREFER_HOST_NAME=hostname -e MODE=standalone -e SPRING_DATASOURCE_PLATFORM=empty -p 8848:8848 nacos/nacos-server
```
## docker mysql启动
### 新建`nacos-standalone`文件夹，新建`standalone.env`文件:
```
PREFER_HOST_NAME=hostname
MODE=standalone
SPRING_DATASOURCE_PLATFORM=mysql
DB.NUM=1
MYSQL_MASTER_SERVICE_HOST=iotechina-dev-mysql-5.7.26
MYSQL_MASTER_SERVICE_DB_NAME=nacos
MYSQL_MASTER_SERVICE_PORT=3306
MYSQL_MASTER_SERVICE_USER=root
MYSQL_MASTER_SERVICE_PASSWORD=you know
```
### 将mysql连接到nacos网络
```
docker network create nacos

docker network connect nacos iotechina-dev-mysql-5.7.26
```
### 启动
```
docker run -d --name nacos --env-file=standalone.env --network nacos  -p 8848:8848 nacos/nacos-server
```
## docker-compose启动
### 新建nacos文件夹
### 新建docker-compose.yml文件
```
version: "3"
networks:
  nacos:
    external: true
services:
  nacos1:
    hostname: nacos1
    container_name: nacos1
    image: nacos/nacos-server:latest
    volumes:
      - ./cluster-logs/nacos1:/home/nacos/logs
      - ./init.d/custom.properties:/home/nacos/init.d/custom.properties
    ports:
      - "8848:8848"
      - "9555:9555"
    env_file:
      - ./env/nacos-hostname.env
    external_links:
	- iotechina-dev-mysql-5.7.26
	networks:
    	- nacos
    restart: on-failure


  nacos2:
    hostname: nacos2
    image: nacos/nacos-server:latest
    container_name: nacos2
    volumes:
      - ./cluster-logs/nacos2:/home/nacos/logs
      - ./init.d/custom.properties:/home/nacos/init.d/custom.properties
    ports:
      - "8849:8848"
    external_links:
	- iotechina-dev-mysql-5.7.26
    env_file:
      - ./env/nacos-hostname.env
    restart: on-failure
	networks:
    	- nacos
  nacos3:
    hostname: nacos3
    image: nacos/nacos-server:latest
    container_name: nacos3
    volumes:
      - ./cluster-logs/nacos3:/home/nacos/logs
      - ./init.d/custom.properties:/home/nacos/init.d/custom.properties
    ports:
      - "8850:8848"
    external_links:
	- iotechina-dev-mysql-5.7.26
    env_file:
      - ./env/nacos-hostname.env
    restart: on-failure
	networks:
    	- nacos    
```
### 复制[官方docker配置](https://github.com/nacos-group/nacos-docker/blob/master/example/cluster-hostname.yaml)到 nacos目录对应的文件夹
### 编辑`nacos/env/nacos-hostname.env`
```
#nacos dev env
PREFER_HOST_MODE=hostname
NACOS_SERVERS=nacos1:8848 nacos2:8848 nacos3:8848
MYSQL_MASTER_SERVICE_HOST=iotechina-dev-mysql-5.7.26
MYSQL_MASTER_SERVICE_DB_NAME= DB_NAME
MYSQL_MASTER_SERVICE_PORT=3306
MYSQL_SLAVE_SERVICE_HOST=iotechina-dev-mysql-5.7.26
MYSQL_SLAVE_SERVICE_PORT=3306
MYSQL_MASTER_SERVICE_USER=you know
MYSQL_MASTER_SERVICE_PASSWORD=you know
```
### mysql 连接 nacos network
```
docker network connect nacos iotechina-dev-mysql-5.7.26
```
### 启动
```
docker-compose up -d
```


## 单机集群mysql启动
~~复制mysql驱动
在`nacos/plugins/`新建文件夹`mysql`,复制对应数据库的jar驱动到这个文件夹~~
### 修改配置文件 
编辑`nacos/application.properties`文件,加入如下内容:
```
spring.datasource.platform=mysql

db.num=1
db.url.0=jdbc:mysql://dev.iotechina.com:13306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=you know
db.password=you know
```
编辑`nacos/conf/cluster.conf`(如果没有新建一个)
```
192.168.0.118:8848  #注意这里和`conf/application.properties`中的`server.port`相对应
192.168.0.118:8847
192.168.0.118:8846
```
### 启动

```
sh startup.sh 
```
# spring cloud
## 版本对应关系
由于nacos还没有纳入spring cloud体系，所以需要手动管理依赖关系。

版本对应关系:[版本说明 Wiki](https://github.com/spring-cloud-incubator/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E)
## 服务发现
### Demo
#### 启动nacos-server

#### 配置应用
##### 新建两个spring-boot应用:service-consumer,service-provider
##### 分别配置两个应用
引入pom依赖:
```
<!--注意这里版本依赖关系，根据自己的springcloud版本引入对应的版本-->
        <dependency>
        	<groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            <version>${spring-cloud-nacos-version}</version>
        </dependency>
```
启动类添加注解：`@EnableDiscoveryClient`

编辑`application.properties`:
```
spring.application.name=${application-name} #应用名称，根据自己需要填写
spring.cloud.nacos.discovery.server-addr=dev.iotechina.com:8848
```
#### 使用feign进行调用测试

## 配置中心
### Demo
#### 启动nacos-server
#### 添加配置
打开nacos网页端:
配置管理->配置列表->点击加号添加配置
```
Data ID: service-consumer.properties #注意，此处一定要有.properties，并且名字和应用的${spring.application.name}一致

Group : DEFAULT_GROUP

配置格式: properties

配置内容: server.port=8845
```
点击发布

#### 应用配置
引入pom依赖
```
<!--注意这里版本依赖关系，根据自己的springcloud版本引入对应的版本-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
            <version>${spring-cloud-nacos-version}</version>
        </dependency>
```
编辑bootstrap.properties:
```properties
spring.application.name=service-consumer
spring.cloud.nacos.config.server-addr=dev.iotechina.com:8848
```
启动应用，发现启动端口已变为8845
```
2019-05-29 15:13:35.493  INFO 1593 --- [           main] o.s.c.a.n.c.NacosPropertySourceBuilder   : Loading nacos data, dataId: 'service-consumer.properties', group: 'DEFAULT_GROUP'
2019-05-29 15:13:35.494  INFO 1593 --- [           main] b.c.PropertySourceBootstrapConfiguration : Located property source: CompositePropertySource {name='NACOS', propertySources=[NacosPropertySource {name='service-consumer.properties'}]}
2019-05-29 15:13:35.498  INFO 1593 --- [           main] c.d.n.NacosConsumerApplication           : No active profile set, falling back to default profiles: default
2019-05-29 15:13:35.974  INFO 1593 --- [           main] o.s.cloud.context.scope.GenericScope     : BeanFactory id=825b518e-28f8-3c01-8579-67d040c18160
2019-05-29 15:13:35.991  INFO 1593 --- [           main] trationDelegate$BeanPostProcessorChecker : Bean 'com.demo.nacosconsumer.feign.EchoFeign' of type [org.springframework.cloud.openfeign.FeignClientFactoryBean] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
2019-05-29 15:13:36.033  INFO 1593 --- [           main] trationDelegate$BeanPostProcessorChecker : Bean 'org.springframework.cloud.autoconfigure.ConfigurationPropertiesRebinderAutoConfiguration' of type [org.springframework.cloud.autoconfigure.ConfigurationPropertiesRebinderAutoConfiguration$$EnhancerBySpringCGLIB$$6f581f58] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
2019-05-29 15:13:36.188  INFO 1593 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8845 (http)
```
### profile粒度的配置
nacos控制台新建service-consumer-test.properties文件，写入内容server.port=8847。

编辑bootstrap.properties:
```
spring.profiles.active=test
```
重启应用，查看端口变化

### 自定义 namespace 的配置
 Nacos 的 Namespace 的概念：
 >用于进行租户粒度的配置隔离。不同的命名空间下，可以存在相同的 Group 或 Data ID 的配置。Namespace 的常用场景之一是不同环境的配置的区分隔离，例如开发测试环境和生产环境的资源（如配置、服务）隔离等。
 
 在没有明确指定 ${spring.cloud.nacos.config.namespace} 配置的情况下， 默认使用的是 Nacos 上 Public 这个namespae。如果需要使用自定义的命名空间，可以通过以下配置来实现：
 ```
 spring.cloud.nacos.config.namespace=b3404bc0-d7dc-4855-b519-570ed34b62d7
 ```
 *** 该配置必须放在 bootstrap.properties 文件中。此外 spring.cloud.nacos.config.namespace 的值是 namespace 对应的 id，id 值可以在 Nacos 的控制台获取。并且在添加配置时注意不要选择其他的 namespae，否则将会导致读取不到正确的配置。 ***
### 自定义 Group 的配置
在没有明确指定 ${spring.cloud.nacos.config.group} 配置的情况下， 默认使用的是 DEFAULT_GROUP 。如果需要自定义自己的 Group，可以通过以下配置来实现：
```
spring.cloud.nacos.config.group=DEVELOP_GROUP
```
*** 该配置必须放在 bootstrap.properties 文件中。并且在添加配置时 Group 的值一定要和 spring.cloud.nacos.config.group 的配置值一致。***

### 自定义扩展的 Data Id 配置

配置Demo：
```
spring.application.name=opensource-service-provider
spring.cloud.nacos.config.server-addr=127.0.0.1:8848

# config external configuration
# 1、Data Id 在默认的组 DEFAULT_GROUP,不支持配置的动态刷新
spring.cloud.nacos.config.ext-config[0].data-id=ext-config-common01.properties

# 2、Data Id 不在默认的组，不支持动态刷新
spring.cloud.nacos.config.ext-config[1].data-id=ext-config-common02.properties
spring.cloud.nacos.config.ext-config[1].group=GLOBALE_GROUP

# 3、Data Id 既不在默认的组，也支持动态刷新
spring.cloud.nacos.config.ext-config[2].data-id=ext-config-common03.properties
spring.cloud.nacos.config.ext-config[2].group=REFRESH_GROUP
spring.cloud.nacos.config.ext-config[2].refresh=true
```
可以看到:
- 通过 `spring.cloud.nacos.config.ext-config[n].data-id` 的配置方式来支持多个 Data Id 的配置。
- 通过 `spring.cloud.nacos.config.ext-config[n].group` 的配置方式自定义 Data Id 所在的组，不明确配置的话，默认是 DEFAULT_GROUP。
- 通过 spring.cloud.nacos.config.ext-config[n].refresh 的配置方式来控制该 Data Id 在配置变更时，是否支持应用中可动态刷新， 感知到最新的配置值。默认是不支持的。

Note: ***多个 Data Id 同时配置时，他的优先级关系是 `spring.cloud.nacos.config.ext-config[n].data-id` 其中 n 的值越大，优先级越高。***

Note:*** `spring.cloud.nacos.config.ext-config[n].data-id` 的值必须带文件扩展名，文件扩展名既可支持 properties，又可以支持 yaml/yml。 此时 spring.cloud.nacos.config.file-extension 的配置对自定义扩展配置的 Data Id 文件扩展名没有影响。***


为了更加清晰的在多个应用间配置共享的 Data Id ，你可以通过以下的方式来配置：
```
spring.cloud.nacos.config.shared-dataids=bootstrap-common.properties,all-common.properties
spring.cloud.nacos.config.refreshable-dataids=bootstrap-common.properties
```

可以看到：
- 通过 `spring.cloud.nacos.config.shared-dataids` 来支持多个共享 Data Id 的配置，多个之间用逗号隔开。
- 通过 `spring.cloud.nacos.config.refreshable-dataids` 来支持哪些共享配置的 Data Id 在配置变化时，应用中是否可动态刷新， 感知到最新的配置值，多个 Data Id 之间用逗号隔开。如果没有明确配置，默认情况下所有共享配置的 Data Id 都不支持动态刷新。

**Note**:*** `通过 spring.cloud.nacos.config.shared-dataids` 来支持多个共享配置的 Data Id 时， 多个共享配置间的一个优先级的关系我们约定：按照配置出现的先后顺序，即后面的优先级要高于前面。 ***

**Note**:*** 通过 1spring.cloud.nacos.config.shared-dataids1 来配置时，Data Id 必须带文件扩展名，文件扩展名既可支持 properties，也可以支持 yaml/yml。 此时 `spring.cloud.nacos.config.file-extension1` 的配置对自定义扩展配置的 Data Id 文件扩展名没有影响。 ***

**Note**:***`spring.cloud.nacos.config.refreshable-dataids` 给出哪些需要支持动态刷新时，Data Id 的值也必须明确给出文件扩展名。***

### 配置的优先级
Spring Cloud Alibaba Nacos Config 目前提供了三种配置能力从 Nacos 拉取相关的配置
- A: 通过 spring.cloud.nacos.config.shared-dataids 支持多个共享 Data Id 的配置
- B: 通过 spring.cloud.nacos.config.ext-config[n].data-id 的方式支持多个扩展 Data Id 的配置
- C: 通过内部相关规则(应用名、应用名+ Profile )自动生成相关的 Data Id 配置

当三种方式共同使用时，他们的一个优先级关系是:A < B < C

# 遇到的问题
- 登陆错误:`io.jsonwebtoken.security.SignatureException: Unable to obtain JCA MAC algorithm 'HmacSHA256': Algorithm HmacSHA256 not available


	解决方法: 

    1.由于是我yay -S 安装的jdk，所以没有JAVA_HOME，解决方案，控制台先执行`export JAVA_HOME=/usr/lib/jvm/openjdk1.8.1`
    然后执行 sh startup.sh -m standalone


    2.设置JAVA_HOME环境变量


    参考连接:

    [https://github.com/alibaba/nacos/issues/711](https://github.com/alibaba/nacos/issues/711)

    [https://github.com/jwtk/jjwt/issues/429](https://github.com/jwtk/jjwt/issues/429)

- 查看服务列表跳会登陆页，浏览器控制台提示401错误

    解决方法:

    清除浏览器缓存

- 集群模式，`cluster.conf`中的ip写`127.0.0.1：8848`报错

解决方法:

    写ip地址，例如:`192.168.0.118`