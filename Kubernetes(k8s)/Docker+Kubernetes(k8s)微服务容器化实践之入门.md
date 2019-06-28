# **Docker+Kubernetes(k8s)微服务容器化实践之入门**

2018.06.13 15:57 3688浏览



![图片描述](https://img.mukewang.com/5b20af9c000152b814080755.png)
![图片描述](https://img.mukewang.com/5b20b08f0001a5bd13171033.png)

### 软件架构

![图片描述](https://img.mukewang.com/5b20b3120001d62e11781653.png)

### 微服务

- Grpc 使用HTTP2,序列化效率最高
- Dubbo使用的是python序列化

![图片描述](https://img.mukewang.com/5b20b43a0001254c09760451.png)

![图片描述](https://img.mukewang.com/5b20c19600014dc624932828.png)

### 微服务优点

![图片描述](https://img.mukewang.com/5b20b571000106fb11791418.png)

![图片描述](https://img.mukewang.com/5b20b8ea0001a0e711792010.png)

### 通讯问题

![图片描述](https://img.mukewang.com/5b20c65d00014ebc25581633.png)

### SpringBoot与微服务

![图片描述](https://img.mukewang.com/5b20cd9c0001adf712403802.png)

> Hystrix（豪猪）：当发现服务方不可用的时候，后续请求不会再访问服务方了，当服务不可用时，可以有选择的给客户一个更好的响应
> ![图片描述](https://img.mukewang.com/5b2119ec00017b3e13142982.png)

### 微服务业务分析

> 单点登录，没有session，无状态

![图片描述](https://img.mukewang.com/5b211bdb0001923104800133.png)

### CICD DevOps

> 持续集成（敏捷）
> ![图片描述](https://img.mukewang.com/5b211e7f0001113e13291740.png)
> ![图片描述](https://img.mukewang.com/5b211e8700011f8c10040725.png)



[Docker](https://www.imooc.com/article/tag/73)[架构](https://www.imooc.com/article/tag/76)