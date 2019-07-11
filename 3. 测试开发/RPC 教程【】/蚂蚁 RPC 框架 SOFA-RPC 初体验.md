![img](https:////upload-images.jianshu.io/upload_images/4236553-d78d6c0b69a5497b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/458/format/webp)

image.png

## 前言

最近蚂蚁金服开源了分布式框架 SOFA，楼主写了一个 demo，体验了一下 SOFA 的功能，SOFA 完全兼容 SpringBoot（当然 Dubbo 也是可以兼容的）。

项目地址：[Alipay ](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Falipay%2F)，该主页有 5 个项目，都是阿里开源的。
 [sofa-boot](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Falipay%2Fsofa-boot)，
 [sofa-rpc](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Falipay%2Fsofa-rpc)，
 [sofa-bolt](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Falipay%2Fsofa-bolt)，
 [sofa-ark](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Falipay%2Fsofa-ark)，
 [sofa-rpc-boot-projects](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Falipay%2Fsofa-rpc-boot-projects)。

## 快速开始

实际上，SOFA-RPC 的官方文档已经详细介绍了如何使用这个 RPC 框架，基于 Netty 的长连接。类似 Dubbo。楼主看这个框架主要是为了学习分布式 RPC 框架的设计。

由于测试例子需要两个项目，我们建一个目录，目录下创建两个 maven module（SpringBoot 项目即可）：



![img](https:////upload-images.jianshu.io/upload_images/4236553-3fcaa5d7615b2db1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/656/format/webp)



一个生产者，一个消费者。

将这两个项目的 pom.xml 中 springBoot 的 parent 标签换成如下：

```
  <parent>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofaboot-dependencies</artifactId>
    <version>2.3.1</version>
  </parent>
```

再增加一个依赖：

```
<dependency>
  <groupId>com.alipay.sofa</groupId>
  <artifactId>rpc-sofa-boot-starter</artifactId>
</dependency>
```

到这里，关于 RPC 框架的依赖和搭建就好了，是不是很简单？

## 接口创建

既然是 RPC 服务，那就需要一个接口，再有一个实现类。我们在提供方这里创建。

```
public interface HelloSyncService {
  String saySync(String string);
}

// 实现类
public class HelloSyncServiceImpl implements HelloSyncService {

  @Override
  public String saySync(String string) {
    return "provider tell you : this is your say: " +  string;
  }
}
```

然后在消费方的 pom.xml 添加对这个接口的依赖。

```
<dependency>
  <groupId>cn.think.in.java</groupId>
  <artifactId>provider</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <scope>compile</scope>
</dependency>
```

有了接口，就需要配置一下。

## 接口配置

首先在提供方这里发布接口。创建一个 xml 文件，名为：rpc-sofa-boot-starter-samples.xml。

文件内容：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:sofa="http://sofastack.io/schema/sofaboot"
  xmlns:context="http://www.springframework.org/schema/context"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://sofastack.io/schema/sofaboot   http://sofastack.io/schema/sofaboot.xsd"
  default-autowire="byName">

  <bean id="helloSyncServiceImpl" class="cn.think.in.java.provider.HelloSyncServiceImpl"/>
  <sofa:service ref="helloSyncServiceImpl" interface="cn.think.in.java.provider.HelloSyncService">
    <sofa:binding.bolt/>
  </sofa:service>
</beans>
```

很简单，发布了一个接口，类似 Spring 的一个 bean。

同时这个接口的协议是 bolt，也就是阿里的 RPC 网络通信框架 solt（基于 Netty 的最佳实践）。

同样的，在消费者的 resource 文件下，也创建一个同名文件。内容稍有不同。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:sofa="http://sofastack.io/schema/sofaboot"
  xmlns:context="http://www.springframework.org/schema/context"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://sofastack.io/schema/sofaboot   http://sofastack.io/schema/sofaboot.xsd"
  default-autowire="byName">

  <sofa:reference id="helloSyncServiceReference" interface="cn.think.in.java.provider.HelloSyncService">
    <sofa:binding.bolt/>
  </sofa:reference>
</beans>
```

通过接口得到一个 bean。

好，接口的配置好了，那么就可以启动测试了。

## 准备测试

测试之前还要做点点工作。

在提供者配置文件 appcation.perproties 中，配置一下端口和程序名称。

```
# server.port=8080 # 默认
spring.application.name=provider
```

默认 8080 端口，就不必配置了。

然后，在消费者那里同样配置这个文件。内容如下：

```
spring.application.name=consumer
server.port=8081
```

消费者和提供者端口不能冲突。

还剩最后一步。

将文件引入到 Spring 容器中。

在提供者启动类上加入以下内容（引入配置文件）：

```
@ImportResource({ "classpath*:rpc-sofa-boot-starter-samples.xml" })
@SpringBootApplication
public class ProviderApplication {

  public static void main(String[] args) {
    SpringApplication.run(ProviderApplication.class, args);
  }
}
```

在消费者启动类中引入以下内容：

```
@ImportResource({ "classpath*:rpc-sofa-boot-starter-samples.xml" })
@SpringBootApplication
public class ConsumerApplication {

  public static void main(String[] args) {
    SpringApplication springApplication = new SpringApplication(ConsumerApplication.class);
    ApplicationContext applicationContext = springApplication.run(args);

    HelloSyncService helloSyncServiceReference = (HelloSyncService) applicationContext
        .getBean("helloSyncServiceReference");

    System.out.println(helloSyncServiceReference.saySync("sync"));
  }
}
```

稍微多点东西，但也还是挺简单的。

首先创建一个 Spring 启动类。然后运行，从 Spirng 容器中获取到 bean（被动态代理封装的远程调用）。然后调用代理方法。

## 运行

先运行提供者：

```
2018-04-23 23:18:24.776  INFO 26654 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/env/{name:.*}],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.EnvironmentMvcEndpoint.value(java.lang.String)
2018-04-23 23:18:24.776  INFO 26654 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/env || /env.json],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.EndpointMvcAdapter.invoke()
2018-04-23 23:18:24.886  INFO 26654 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2018-04-23 23:18:24.893  INFO 26654 --- [           main] o.s.c.support.DefaultLifecycleProcessor  : Starting beans in phase 0
Sofa-Middleware-Log SLF4J : Actual binding is of type [ com.alipay.remoting Logback ]
2018-04-23 23:18:24.966  INFO 26654 --- [           main] com.alipay.sofa.common.log               : Sofa-Middleware-Log SLF4J : Actual binding is of type [ com.alipay.remoting Logback ]
2018-04-23 23:18:25.174  INFO 26654 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2018-04-23 23:18:25.179  INFO 26654 --- [           main] c.t.i.java.provider.ProviderApplication  : Started ProviderApplication in 3.352 seconds (JVM running for 3.978)
```

再运行消费者：

```
2018-04-23 23:19:21.940  INFO 26673 --- [           main] o.s.b.a.e.mvc.EndpointHandlerMapping     : Mapped "{[/env || /env.json],methods=[GET],produces=[application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.mvc.EndpointMvcAdapter.invoke()
2018-04-23 23:19:22.055  INFO 26673 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2018-04-23 23:19:22.063  INFO 26673 --- [           main] o.s.c.support.DefaultLifecycleProcessor  : Starting beans in phase 0
2018-04-23 23:19:22.319  INFO 26673 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8081 (http)
2018-04-23 23:19:22.324  INFO 26673 --- [           main] c.t.i.java.consumer.ConsumerApplication  : Started ConsumerApplication in 3.898 seconds (JVM running for 4.524)
provider tell you : this is your say: sync
```

成功打印结果。

## 总结

首先第一感觉是，这个框架还是挺好用，挺简单的，基于当前的 SpringBoot 。快速启动。而且不是 SpringCloud 的 Http 调用，使用 Netty 作为网络通信框架，性能当然是没有问题的。

当然，我们这里的 demo 使用的注册中心没有使用 ZK，毕竟初体验嘛，使用的本地的文件。

然而，楼主对这个框架有很大的兴趣。接下来的空闲时间里，楼主将好好研究 SOFA 相关的代码。

作者：莫那一鲁道

链接：https://www.jianshu.com/p/d986dd9b1e63

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。