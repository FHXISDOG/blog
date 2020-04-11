---
title: "skywalking使用指南"
date: 2019-05-29T15:33:57+08:00
draft: false 
categories: ["skywalking"]
tags: ["java","skywalking","spring cloud"]
---

# 运行

## 单机运行
- 下载最新[release](http://skywalking.apache.org/downloads/)包
- 解压，sh ${skywalkingpath}/bin/startup.sh
- 在`springboot`启动命令前加入启动参数
```
-javaagent:${path}/skywalking-agent.jar  #path为skywalking解压的agent目录
```
- 访问localhost:8080查看结果

## docker 运行

### 前置条件
#### 下载
下载最新[release](http:/skywalking.apache.org/downloads/)包并解压

#### 构建docker bridge
```
dcoker network create skywalking
```
#### 使用数据库
##### ElasticSearch
###### 启动es

``` 
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:6.4.3 #skywalking目前只支持6.x(6.1.0版本)
```

###### 初始化数据库
编辑 ```${path}/apache-skywalking-bin/config/appliccation.yml```

```
storage:
  #elasticsearch:
    nameSpace: ${SW_NAMESPACE:""}
    clusterNodes: ${SW_STORAGE_ES_CLUSTER_NODES:dev.iotechina.com:9201}
    #user: ${SW_ES_USER:""}
    #password: ${SW_ES_PASSWORD:""}
    indexShardsNumber: ${SW_STORAGE_ES_INDEX_SHARDS_NUMBER:2}
    indexReplicasNumber: ${SW_STORAGE_ES_INDEX_REPLICAS_NUMBER:0}
    #Batch process setting, refer to https://www.elastic.co/guide/en/elasticsearch/client/java-api/5.5/java-docs-bulk-processor.html
    bulkActions: ${SW_STORAGE_ES_BULK_ACTIONS:2000} # Execute the bulk every 2000 requests
    bulkSize: ${SW_STORAGE_ES_BULK_SIZE:20} # flush the bulk every 20mb
    flushInterval: ${SW_STORAGE_ES_FLUSH_INTERVAL:10} # flush the bulk every 10 seconds whatever the number of requests
    concurrentRequests: ${SW_STORAGE_ES_CONCURRENT_REQUESTS:2} # the number of concurrent requests
    metadataQueryMaxSize: ${SW_STORAGE_ES_QUERY_MAX_SIZE:5000}
    segmentQueryMaxSize: ${SW_STORAGE_ES_QUERY_SEGMENT_SIZE:200}

```

执行

```
sh ${path}/apache-skywalking-bin/bin/oapServiceInit.sh 

```


执行

```

tail -f ${path}/apache-skywalking-bin/config/skywalking-oap-server.log

```
等待执行完成

##### mysql
###### 复制mysql驱动到${path}/apapch-skywalking-bin/oap-libs
###### 编辑 ${path}/apache-skywalking-bin/config/appliccation.yml

```

storage:
  mysql:
    metadataQueryMaxSize: ${SW_STORAGE_H2_QUERY_MAX_SIZE:5000}


```

###### 编辑${path}/apache-skywalking-bin/config/datasource-setting.properties

```

jdbcUrl=jdbc:mysql://dev.iotechina.com:3316/sky_walking
dataSource.user= you know
dataSource.password=you know
dataSource.cachePrepStmts=true
dataSource.prepStmtCacheSize=250
dataSource.prepStmtCacheSqlLimit=2048
dataSource.useServerPrepStmts=true
dataSource.useLocalSessionState=true
dataSource.rewriteBatchedStatements=true
dataSource.cacheResultSetMetadata=true
dataSource.cacheServerConfiguration=true
dataSource.elideSetAutoCommits=true
dataSource.maintainTimeStats=false

```


```
sh ${path}/apache-skywalking-bin/bin/oapServiceInit.sh 

```

执行 

```
tail -f ${path}/apache-skywalking-bin/config/skywalking-oap-server.log

```
等待执行完成

### 构建OAP镜像
#### 准备条件


复制`oap-libs`文件夹到`/root/skywalking/oap`目录

复制`config`目录到`/root/skywalking/oap`目录

编辑`config`目录下`log4j2.xml`：
```
<?xml version="1.0" encoding="UTF-8"?>

<Configuration status="INFO">
    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout charset="UTF-8" pattern="%d - %c -%-4r [%t] %-5p %x - %m%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <logger name="org.eclipse.jetty" level="INFO"/>
        <logger name="org.apache.zookeeper" level="INFO"/>
        <logger name="org.elasticsearch.common.network.IfConfig" level="INFO"/>
        <logger name="io.grpc.netty" level="INFO"/>
        <logger name="org.apache.skywalking.oap.server.receiver.istio.telemetry" level="DEBUG"/>
        <Root level="INFO">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</Configuration>

```

#### 编写启动脚本
新建`docker-entrypoint.sh`:
```
#!/bin/bash

set -ex

CLASSPATH="config:$CLASSPATH"
for i in oap-libs/*.jar
do
    CLASSPATH="$i:$CLASSPATH"
done

exec java -Xms256M -Xmx512M -classpath $CLASSPATH org.apache.skywalking.oap.server.starter.OAPServerStartUp "$@"

```

#### 编写Dockerfile
新建`Dcokerfile`:
```
FROM openjdk:8-jre-alpine
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
RUN echo 'Asia/Shanghai' >/etc/timezone  #设置时区，不然ui会查不到数据
WORKDIR /skywalking
RUN apk add --no-cache \
    bash
COPY docker-entrypoint.sh /skywalking
COPY oap-libs/ /skywalking/oap-libs
EXPOSE 12800 11800
ENTRYPOINT ["sh","docker-entrypoint.sh"]
```

#### 构建镜像
```
dcoker build -t {your tag} .
```
#### 启动容器
```
docker run --name skywalking-oap -d -v /root/skywalking/oap/config:/skywalking/config -p 12800:12800 -p  11800:11800 --network skywalking {your tag}
```

### 构建ui镜像

#### 准备条件
复制`webapp`文件夹到`/root/skywalking/ui`目录

在`/root/skywalking/ui/webapp`目录下新建文件`logback.xml`：
```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/base.xml" />
</configuration>
```

#### 编写启动脚本:
新建`docker-entrypoint.sh`：
```
#!/bin/bash

exec java -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap -jar /skywalking/webapp/skywalking-webapp.jar --logging.config=/skywalking/webapp/logback.xml  --spring.config.location=/skywalking/webapp/webapp.yml "$@"
```

#### 编写Dockerfile:
新建`Dockerfile`：
```
FROM openjdk:8-jre-alpine


RUN apk add --no-cache \
    bash

RUN echo 'Asia/Shanghai' >/etc/timezone
WORKDIR /skywalking


COPY docker-entrypoint.sh .

EXPOSE 8080

ENTRYPOINT ["bash", "docker-entrypoint.sh"]

```

#### 构建镜像
```
dcoker build -t  {your tag} .
```

#### 修改配置
编辑`/root/skywalking/webapp/webapp.yml`
```
server:
  port: 8080

collector:
  path: /graphql
  ribbon:
    ReadTimeout: 10000
    # Point to all backend's restHost:restPort, split by ,
    listOfServers: skywalking-oap:12800  #服务端地址
security:
    user:
        admin:
            password: you know
```
#### 启动容器
```
 docker run --name skywalking-ui -d -v /root/skywalking/ui/webapp:/skywalking/webapp -p 8080:8080 --network skywalking  {your tag}
```

#### 修改agent配置
修改 `path/agent/config/agent.config`：
```
collector.backend_service=${SW_AGENT_COLLECTOR_BACKEND_SERVICES:你的服务器地址:11800}

```

#### 启动应用并访问服务器8080端口查看信息

## 连接`elasticsearch`(基于上一步docker运行skywalking)
### 启动`esasticsearch`
```
 docker run -d --name es-skywalking  -p 9201:9200 -p 9301:9300 -e "discovery.type=single-node" elasticsearch:6.4.3  #skywalking 6.1.0 貌似不支持 es 7.x的版本
```
### 初始化`elasticsearch`
修改本地 `path/config/application.yml`:
```
storage:
  elasticsearch:
    #nameSpace: ${SW_NAMESPACE:""}
    clusterNodes: ${SW_STORAGE_ES_CLUSTER_NODES:你的服务器地址:9201}
    #user: ${SW_ES_USER:""}
    #password: ${SW_ES_PASSWORD:""}
    indexShardsNumber: ${SW_STORAGE_ES_INDEX_SHARDS_NUMBER:2}
    indexReplicasNumber: ${SW_STORAGE_ES_INDEX_REPLICAS_NUMBER:0}
    # Batch process setting, refer to https://www.elastic.co/guide/en/elasticsearch/client/java-api/5.5/java-docs-bulk-processor.html
    bulkActions: ${SW_STORAGE_ES_BULK_ACTIONS:2000} # Execute the bulk every 2000 requests
    bulkSize: ${SW_STORAGE_ES_BULK_SIZE:20} # flush the bulk every 20mb
    flushInterval: ${SW_STORAGE_ES_FLUSH_INTERVAL:10} # flush the bulk every 10 seconds whatever the number of requests
    concurrentRequests: ${SW_STORAGE_ES_CONCURRENT_REQUESTS:2} # the number of concurrent requests
    metadataQueryMaxSize: ${SW_STORAGE_ES_QUERY_MAX_SIZE:5000}
    segmentQueryMaxSize: ${SW_STORAGE_ES_QUERY_SEGMENT_SIZE:200}
```

执行
```
sh {path}/bin/oapServiceInit.sh 
```
### 将`es`接入 skywalking bridge
``` 
docker network connect skywalking es-skywalking
```
### 修改`skywalking`配置文件
修改`/root/skywalking/oap/config/application.yml`：
```
storage:
  elasticsearch:
    #nameSpace: ${SW_NAMESPACE:""}
    clusterNodes: ${SW_STORAGE_ES_CLUSTER_NODES:es-skywalking:9200}
    #user: ${SW_ES_USER:""}
    #password: ${SW_ES_PASSWORD:""}
    indexShardsNumber: ${SW_STORAGE_ES_INDEX_SHARDS_NUMBER:2}
    indexReplicasNumber: ${SW_STORAGE_ES_INDEX_REPLICAS_NUMBER:0}
    # Batch process setting, refer to https://www.elastic.co/guide/en/elasticsearch/client/java-api/5.5/java-docs-bulk-processor.html
    bulkActions: ${SW_STORAGE_ES_BULK_ACTIONS:2000} # Execute the bulk every 2000 requests
    bulkSize: ${SW_STORAGE_ES_BULK_SIZE:20} # flush the bulk every 20mb
    flushInterval: ${SW_STORAGE_ES_FLUSH_INTERVAL:10} # flush the bulk every 10 seconds whatever the number of requests
    concurrentRequests: ${SW_STORAGE_ES_CONCURRENT_REQUESTS:2} # the number of concurrent requests
    metadataQueryMaxSize: ${SW_STORAGE_ES_QUERY_MAX_SIZE:5000}
    segmentQueryMaxSize: ${SW_STORAGE_ES_QUERY_SEGMENT_SIZE:200}

```
### 重启`skywalking-oap`
```
docker restart skywalking-oap 
```

# plugin使用
## apm-toolkit-logback-1.x
### 项目pom文件引入
```
        <dependency>
            <groupId>org.apache.skywalking</groupId>
            <artifactId>apm-toolkit-logback-1.x</artifactId>
            <version>6.1.0</version>
        </dependency>
```
### 编辑logback.yml
```
   <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.TraceIdPatternLogbackLayout">
                <Pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%tid] [%thread] %-5level %logger{36} -%msg%n</Pattern>
            </layout>
        </encoder>
    </appender>
```
