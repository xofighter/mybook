swagger是一个API框架，号称世界上最流行的API工具。它提供了API管理的全套解决方案，比如API在线编辑器，API UI展示界面，代码生成器等诸多功能。

如果想引入swagger进行API管理。目前 springfox 是一个很好的选择，它内部会自动解析Spring容器中Controller暴露出的接口，并且也提供了一个界面用于展示或调用这些API。下图就是简单的一个使用springfox的API展示界面。



![img](https:////upload-images.jianshu.io/upload_images/2509688-ffab53d1e0dc063b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



springfox的前身是swagger-springmvc，用于springmvc与swagger的整合。

如若在springboot项目中使用springfox，需要3个步骤：

1、maven添加springfox依赖

2、启动类加上@EnableSwagger2注解

3、构造Docket bean用于展示API

配置完之后进入 [http://](https://yq.aliyun.com/articles/599809?utm_content=m_1000002417){path}:{port}/swagger-ui.html 即可查看controller中的接口信息，并按照Docket中配置的规则进行展示。

#### springfox实现原理

在分析springfox实现原理之前，首先看下springfox对文档Documentation的定义：



![img](https:////upload-images.jianshu.io/upload_images/2509688-d3a18d9a138c9492.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



文档Documentation定义得很清晰，主要由groupName(分组名)、basePath(contextPath)、apiListings(API列表集)、resourceListing(资源列表集)等属性组成。

其中API列表被封装成ApiListing。ApiListing中又持有ApiDesciption集合引用，每个ApiDesciption都持有一个API集合的引用，Operation也就是具体的接口操作，内部包含了该接口对应的http方法、produces、consumes、协议、参数集、响应消息集等诸多元素。

**springfox通过spring-plugin的方式将Plugin注册到Spring上下文中，然后使用这些plugin进行API的扫描工作，这里的扫描工作其实也就是构造Documentation的工作，把扫描出的结果封装成Documentation并放入到DocumentationCache内存缓存中，之后swagger-ui界面展示的API信息通过Swagger2Controller暴露，Swagger2Controller内部直接从DocumentationCache中寻找Documentation。**

下图就是部分Plugin具体构造对应的文档信息：



![img](https:////upload-images.jianshu.io/upload_images/2509688-e73bb905db99b476.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/882/format/webp)



代码细节方面的分析：

很明显，入口处在@EnableSwagger2注解上，该注解会import一个配置类Swagger2DocumentationConfiguration。

Swagger2DocumentationConfiguration做的事情：

1、构造Bean。比如HandlerMapping，HandlerMapping是springmvc中用于处理请求与handler(controller中的方法)之间映射关系的接口，springboot中默认使用的HandlerMapping是RequestMappingHandlerMapping，Swagger2DocumentationConfiguration配置类里构造的是PropertySourcedRequestMappingHandlerMapping，该类继承RequestMappingHandlerMapping。

2、import其它配置类，比如SpringfoxWebMvcConfiguration、SwaggerCommonConfiguration

3、扫描指定包下的类，并注册到Spring上下文中

SpringfoxWebMvcConfiguration配置类做的事情跟Swagger2DocumentationConfiguration类似，不过多了一步构造PluginRegistry过程。该过程使用@EnablePluginRegistries注解实现：



![img](https:////upload-images.jianshu.io/upload_images/2509688-164467c3c7fe1e7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/782/format/webp)



@EnablePluginRegistries注解是spring-plugin模块提供的一个基于Plugin类型注册PluginRegistry实例到Spring上下文的注解。

@EnablePluginRegistries注解内部使用PluginRegistriesBeanDefinitionRegistrar注册器去获取注解的value属性(类型为Plugin接口的Class数组)；然后遍历这个Plugin数组，针对每个Plugin在Spring上下文中注册PluginRegistryFactoryBean，并设置相应的name和属性。

如果处理的Plugin有@Qualifier注解，那么这个要注册的PluginRegistryFactoryBean的name就是@Qualifier注解的value，否则name就是插件名首字母小写+Registry的格式(比如DocumentationPlugin对应构造的bean的name就是documentationPluginRegistry)。

PluginRegistriesBeanDefinitionRegistrar注册器处理过程：



![img](https:////upload-images.jianshu.io/upload_images/2509688-9021331af9f7bf77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/654/format/webp)



PluginRegistryFactoryBean是一个FactoryBean，其内部真正构造的bean的类型是OrderAwarePluginRegistry。OrderAwarePluginRegistry实例化过程中会调用create静态方法，传入的plugin集合使用aop代理生成一个ArrayList，这个list中的元素就是Spring上下文中所有的类型为之前遍历的Plugin的bean。

PluginRegistryFactoryBean的getObject方法：



![img](https:////upload-images.jianshu.io/upload_images/2509688-ec844671eac283f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/781/format/webp)





![img](https:////upload-images.jianshu.io/upload_images/2509688-8b16c7415093b372.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/786/format/webp)



这里的targetSource是在PluginRegistryFactoryBean的父类AbstractTypeAwareSupport(实现了InitializingBean接口)中的afterPropertiesSet方法中初始化的(type属性在PluginRegistriesBeanDefinitionRegistrar注册器中已经设置为遍历的Plugin)：



![img](https:////upload-images.jianshu.io/upload_images/2509688-af989299ad4117e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/783/format/webp)



BeansOfTypeTargetSource的getTarget方法：



![img](https:////upload-images.jianshu.io/upload_images/2509688-9f4822a829ccb450.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/728/format/webp)



举个例子：比如SpringfoxWebMvcConfiguration中的@EnablePluginRegistries注解里的DocumentationPlugin这个Plugin，在处理过程中会找出Spring上下文中所有的Docket(Docket实现了DocumentationPlugin接口)，并把该集合设置成name为documentationPluginRegistry、类型为OrderAwarePluginRegistry的bean，注册到Spring上下文中。

DocumentationPluginsManager类会在之前提到过的配置类中被扫描出来，它内部的各个pluginRegistry属性都是@EnablePluginRegistries注解内部构造的各种pluginRegistry实例：



![img](https:////upload-images.jianshu.io/upload_images/2509688-8b717981f0f2725d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/787/format/webp)



DocumentationPluginsBootstrapper启动类也会在之前提供的配置类中被扫描出来。它实现了SmartLifecycle接口，在start方法中，会获取之前初始化的所有documentationPlugins(也就是Spring上下文中的所有Docket)。遍历这些Docket并进行scan扫描(使用RequestMappingHandlerMapping的getHandlerMethods方法获取url与方法的所有映射关系，然后进行一系列API解析操作)，扫描出来的结果封装成Documentation并添加到DocumentationCache中：



![img](https:////upload-images.jianshu.io/upload_images/2509688-629fd1224f4f8055.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/786/format/webp)



以上就是API解析、扫描的大致处理过程，整理如下：



![img](https:////upload-images.jianshu.io/upload_images/2509688-a2787304625ac616.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



下面分析一下HandlerMapping的处理过程。

PropertySourcedRequestMappingHandlerMapping在Swagger2DocumentationConfiguration配置类中被构造：



![img](https:////upload-images.jianshu.io/upload_images/2509688-d5e0dade8bbcb8a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/786/format/webp)



PropertySourcedRequestMappingHandlerMapping初始化过程中会设置优先级为Ordered.HIGHEST_PRECEDENCE + 1000，同时还会根据Swagger2Controller得到RequestMappingInfo映射信息，并设置到handlerMethods属性中。

PropertySourcedRequestMappingHandlerMapping复写了lookupHandlerMethod方法，首先会去handlerMethods属性中查询是否存在对应的映射关系，没找到的话使用下一个HandlerMapping进行处理：



![img](https:////upload-images.jianshu.io/upload_images/2509688-a1996958361fdfe0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/733/format/webp)



Swagger2Controller中只有一个mapping方法，默认的path值为/v2/api-docs，可以通过配置 springfox.documentation.swagger.v2.path 进行修改。所以默认情况下 /v2/api-docs?group=person-api、/v2/api-docs?group=user-api 这些地址都会被Swagger2Controller所处理。

Swagger2Controller内部获取文档信息会去DocumentationCache中查找：



![img](https:////upload-images.jianshu.io/upload_images/2509688-212db732e944e1ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/734/format/webp)



#### 引入springfox带来的影响

影响主要有2点：

应用启动速度变慢，因为额外加载了springfox中的信息，同时内存中也缓存了这些API信息

多了一个HandlerMapping，并且优先级高。以下是springboot应用DispatcherServlet的HandlerMapping集合。其中springfox构造的PropertySourcedRequestMappingHandlerMapping优先级最高。优先级最高说明第一次查询映射关系都是走PropertySourcedRequestMappingHandlerMapping，而程序中大部分请求都是在RequestMappingHandlerMapping中处理的



![img](https:////upload-images.jianshu.io/upload_images/2509688-b26404ddaacb66ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/590/format/webp)



优先级问题可以使用BeanPostProcessor处理，修改优先级：



![img](https:////upload-images.jianshu.io/upload_images/2509688-b53155d22a9eb4a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/733/format/webp)



本文作者：中间件小哥

[阅读原文](http://click.aliyun.com/m/1000002417/)

本文为云栖社区原创内容，未经允许不得转载。

作者：阿里云云栖社区

链接：https://www.jianshu.com/p/a6810cd9c3c0

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。