# Spring Cloud Eureka
目录：
- [Eureka介绍](#Eureka介绍)
- [单节点注册中心](#单节点注册中心)
- [Eureka集群搭建](#Eureka集群)
## Eureka介绍
```java
Eureka is a REST (Representational State Transfer) based service that is primarily used in the AWS cloud for locating services for the purpose of load balancing and failover of middle-tier servers.
Eureka云端服务发现，是一个基于 REST 的服务，用于定位服务，以实现云端中间层服务发现和故障转移。

简单说，eureka是一个服务注册中心，具有服务注册和发现功能，在此基础上可以更好的管理服务，
并且配合SpringCloud其他组件，如Zuul、Hystrix、Bus、Feign等，来更加高效、便利地治理微服务环境
```
## 单节点注册中心

引入maven依赖：
```xml
<!--父依赖-->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.4.5.RELEASE</version>
    <relativePath/>
</parent>

<!--父依赖-->
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka-server</artifactId>
    </dependency>
</dependencies>

<!--springcloud依赖组件-->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Brixton.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<!--springboot 打包插件-->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```
配置文件 application.yml (yaml形式，或者application.properties，参考springboot配置说明)
```yaml
server:
  port: 9999
eureka:
  instance:
    hostname: localhost
  client:
    #由于该应用为注册中心,所以设置为false,代表不向注册中心注册自己
    registerWithEureka: false
    #由于注册中心的职责就是维护服务实例,它并不需要去检索服务,所以也设置为false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/ #注册中心地址 注意：这里需要加http:// 开头，不然页面会报错
```
添加springboot启动类
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@EnableEurekaServer //eureka支持
@SpringBootApplication //springboot支持
public class EurekaApp {
  public static void main(String[] args) {
      SpringApplication.run(EurekaApp.class, args);
  }
}
```
启动后，访问 http://localhost:9999/，效果：
![image](https://wx4.sinaimg.cn/mw690/006YXPCQly1fu85buhlopj31fx0qz0ub.jpg)

## Eureka集群
```java
如果Eureka服务挂了，那么其他注册到上面的服务也将不可用。
Eureka可以多节点配置，也就是说每个Eureka可以作为单独的节点，并且这些节点互相注册互相作为注册方和消费方。
这样其中一个几点挂了，其他节点照样能提供服务。
```
项目演示：

在application.yml的基础上，新建配置文件：application-pee1.yml application-pee2.yml application-pee3.yml

application.yml：
```yaml
eureka:
  instance:
    hostname: localhost
  client:
    #默认为ture，代表向注册中心注册自己
    #registerWithEureka: false
    #默认为ture，从注册中心获取服务信息
    #fetchRegistry: false
spring:
  profiles:
    active: pee1 #默认启动pee1环境的配置
```
application-pee1.yml配置
```yaml
server:
  port: 9901
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:9902/eureka/,http://localhost:9903/eureka/ #注册中心地址为其他2个节点地址
spring:
  application:
    name: eureka-server1 #服务名称
```
pee1和pee2配置
```yaml
pee2同pee1，将端口改为9902，注册中心地址改为 http://9901,http://9903
pee3同pee1，将端口改为9903，注册中心地址改为 http://9901,http://9902
```
分别启动pee1 pee2
```java
启动startApp后，将application.yml中的active分别改为pee2，pee3，分别启动
然后分别访问http://localhost:9901 http://localhost:9902 http://localhost:9903
```
效果如下：
![image](https://wx4.sinaimg.cn/mw690/006YXPCQly1fu85sf88jxj31fv0q9tap.jpg)

## 源码地址
[https://github.com/yangzhenlong/springCloudDemo](https://github.com/yangzhenlong/springCloudDemo)