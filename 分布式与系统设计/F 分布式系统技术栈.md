[TOC]

### 微服务技术栈

#### 微服务开发

作用：快速开发服务。

* Spring
* Spring MVC
* Spring Boot

Spring 目前是 JavaWeb 开发人员必不可少的一个框架，SpringBoot 简化了 Spring 开发的配置目前也是业内主流开发框架。

#### 微服务注册发现

作用：发现服务，注册服务，集中管理服务。

##### 1. Eureka

* Eureka Server : 提供服务注册服务, 各个节点启动后，会在 Eureka Server 中进行注册。
* Eureka Client : 简化与 Eureka Server 的交互操作
* Spring Cloud Netflix : [GitHub](https://github.com/spring-cloud/spring-cloud-netflix)，[文档](https://cloud.spring.io/spring-cloud-netflix/reference/html/)。

##### 2. Zookeeper

Zookeeper 是一个集中的服务, 用于维护配置信息、命名、提供分布式同步和提供组服务。

**Zookeeper 和 Eureka 区别**：

Zookeeper 保证 **CP**，Eureka 保证 **AP**：

* C：数据一致性。
* A：服务可用性。
* P：服务对网络分区故障的容错性，这三个特性在任何分布式系统中不能同时满足，最多同时满足两个。

#### 微服务配置管理

作用：统一管理一个或多个服务的配置信息，集中管理。

##### 1. Disconf

Distributed Configuration Management Platform(分布式配置管理平台) ，它是专注于各种分布式系统配置管理 的通用组件/通用平台，提供统一的配置管理服务，是一套完整的基于 zookeeper 的分布式配置统一解决方案。

* [GitHub](https://github.com/knightliao/disconf)

##### 2. SpringCloudConfig

* [GitHub](https://github.com/spring-cloud/spring-cloud-config)

##### 3. Apollo

Apollo（阿波罗）是**携程**框架部门研发的分布式配置中心，能够集中化管理应用不同环境、不同集群的配置，配置修改后能够实时推送到应用端，并且具备规范的权限、流程治理等特性，用于微服务配置管理场景。

* [GitHub](https://github.com/ctripcorp/apollo)

#### 权限认证

作用：根据系统设置的安全规则或者安全策略, 用户可以访问而且只能访问自己被授权的资源，不多不少。

##### 1. Spring Security

* [官网](https://spring.io/projects/spring-security)

##### 2. apache Shiro

* [官网](http://shiro.apache.org/)

#### 批处理

作用: 批量处理同类型数据或事物。

##### 1. Spring Batch

* [官网](官网)

#### 定时任务

定时做任务。

##### 1. Quartz

* [官网](http://www.quartz-scheduler.org/)

#### 微服务调用 (协议)

通讯协议。

##### 1. Rest

* 通过 HTTP/HTTPS 发送 Rest 请求进行数据交互

##### 2. RPC

Remote Procedure Call。它是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。RPC 不依赖于具体的网络传输协议，tcp、udp 等都可以。

##### 3. gRPC

所谓 RPC(remote procedure call 远程过程调用) 框架实际是提供了一套机制，使得应用程序之间可以进行通信，而且也遵从 server/client 模型。使用的时候客户端调用 server 端提供的接口就像是调用本地的函数一样。

* [官网](https://www.grpc.io/)


##### 4. RMI

Remote Method Invocation，纯 Java 调用。

#### 服务接口调用

多个服务之间的通讯。

##### 1. Feign(HTTP)

Spring Cloud Netflix 的微服务都是以 HTTP 接口的形式暴露的，所以可以用 Apache 的 HttpClient 或 Spring 的 RestTemplate 去调用，而 Feign 是一个使用起来更加方便的 HTTP 客戶端，使用起来就像是调用自身工程的方法，而感觉不到是调用远程方法。

* [GitHub](https://github.com/OpenFeign/feign)

#### 服务熔断

当请求到达一定阈值时不让请求继续。

##### 1. Hystrix

* [GitHub](https://github.com/Netflix/Hystrix)

##### 2. Sentinel

轻量级的流量控制、熔断降级 Java 库。

* [GitHub](https://github.com/alibaba/Sentinel)

#### 服务负载均衡

降低服务压力, 增加吞吐量。

##### 1. Ribbon

Spring Cloud Ribbon 是一个基于 HTTP 和 TCP 的客户端负载均衡工具，它基于 Netflix Ribbon 实现。

* [GitHub](https://github.com/Netflix/ribbon)

##### 2. Nginx

Nginx (engine x) 是一个高性能的 HTTP 和反向代理 web 服务器。

* [GitHub](https://github.com/nginx/nginx)

**Nginx 与 Ribbon 区别**：Nginx 属于**服务端负载均衡**，Ribbon 属于**客户端负载均衡**。Nginx 作用于 Tomcat，Ribbon 作用与各个服务之间的调用 (RPC)。

#### 消息队列

作用：解耦业务，异步化处理数据。

##### 1. Kafka

* [官网](http://kafka.apache.org/)

##### 2. RabbitMQ

* [官网](https://www.rabbitmq.com/)

##### 3. RocketMQ

* [官网](http://rocketmq.apache.org/)

##### 4. ActiveMQ

* [官网](http://activemq.apache.org/)

#### 日志采集 (elk)

收集各服务日志提供日志分析、用户画像等。

##### 1. Elasticsearch

* [GitHub](https://github.com/elastic/elasticsearch)

##### 2. Logstash

* [GitHub](https://github.com/elastic/logstash)

##### 3. Kibana

* [GitHub](https://github.com/elastic/kibana)

#### API网关

外部请求通过 API 网关进行拦截处理，再转发到真正的服务。

##### 1. Zuul

* [GitHub](https://github.com/Netflix/zuul)

#### 服务监控

以可视化或非可视化的形式展示出各个服务的运行情况 (CPU、内存、访问量等)。

##### 1. Zabbix

* [GitHub](https://github.com/jjmartres/Zabbix)

##### 2. Nagios

* [官网](https://www.nagios.org/)

##### 3. Metrics

* [官网](https://metrics.dropwizard.io)

#### 服务链路追踪

明确服务之间的调用关系。

##### 1. Zipkin

* [GitHub](https://github.com/openzipkin/zipkin)

##### 2. Brave

* [GitHub](https://github.com/openzipkin/brave)

#### 数据存储

存储数据。

##### 1. 关系型数据库

###### (1) MySql

* [官网](https://www.mysql.com/)

###### (2) Oracle

* [官网](https://www.oracle.com/index.html)

##### 2. 非关系型数据库

###### (1) MongoDB

* [官网](https://www.mongodb.com/)

###### (2) Elasticsearch

* [GitHub](https://github.com/elastic/elasticsearch)

#### 缓存

存储数据。

##### 1. Redis

* [官网](https://redis.io/)

#### 分库分表

数据库分库分表方案。

##### 1. Shardingsphere

* [官网](http://shardingsphere.apache.org/)

##### 2. Mycat

* [官网](http://www.mycat.io/)

#### 服务部署

将项目快速部署、上线、持续集成。

##### 1. Docker

* [官网](http://www.docker.com/)

##### 2. Jenkins

* [官网](https://jenkins.io/zh/)

##### 3. Kubernetes(K8s)

* [官网](https://kubernetes.io/)



