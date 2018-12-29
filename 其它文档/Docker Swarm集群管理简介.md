## 目录

1. Swarm简介
2. Swarm架构
3. Swarm命令
4. 总结

## 1. Swarm简介

Docker自诞生以来，其容器特性以及镜像特性给DevOps爱好者带来了诸多方便。然而在很长的一段时间内，Docker只能在单host上运行，其跨host的部署、运行与管理能力颇受外界诟病。跨host能力的薄弱，直接导致Docker容器与host的紧耦合，这种情况下，Docker容器的灵活性很难令人满意，容器的迁移、分组等都成为很难实现的功能点。

Swarm是Docker公司在2014年12月初新发布的容器管理工具。和Swarm一起发布的Docker管理工具还有Machine以及Compose。

Swarm是一套较为简单的工具，用以管理Docker集群，使得Docker集群暴露给用户时相当于一个虚拟的整体。Swarm使用标准的Docker API接口作为其前端访问入口，换言之，各种形式的Docker Client(dockerclient in go, docker_py, docker等)均可以直接与Swarm通信。Swarm几乎全部用Go语言来完成开发，并且还处于一个Alpha版本，目前在github上发布的版本仅有v0.1.0-rc1。然而Swarm的发展十分快速，功能和特性的变更迭代还非常频繁。因此，可以说Swarm还不推荐被用于生产环境中，但可以肯定的是Swarm是一项很有前途的技术。

Swarm的设计和其他Docker项目一样，遵循“batteries included but removable”原则。笔者对该原则的理解是：batteries included代表设计Swarm时，为了完全体现分布式容器集群部署、运行与管理功能的完整性，Swarm和Docker协同工作，Swarm内部包含了一个较为简易的调度模块，以达到对Docker集群调度管理的效果；“but  removable”意味着Swarm与Docker并非紧耦合，同时Swarm中的调度模块同样可以定制化，用户可以按照自己的需求，将其替换为更为强大的调度模块，如Mesos等。另外，这套管理引擎并未侵入Docker的使用，这套机制也为其他容器技术的集群部署、运行与管理方式提供了思路。

## 2. Swarm架构

Swarm作为一个管理Docker集群的工具，首先需要将其部署起来，可以单独将Swarm部署于一个节点。另外，自然需要一个Docker集群，集群上每一个节点均安装有Docker。具体的Swarm架构图可以参照下图：



![img](https:////upload-images.jianshu.io/upload_images/2066703-7789fc32d773c102.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

Swarm架构图

Swarm架构中最主要的处理部分自然是Swarm节点，Swarm管理的对象自然是Docker Cluster, Docker Cluster由多个Docker Node组成，而负责给Swarm发送请求的是Docker Client。

## 3.Swarm命令

Swarm架构图可以让大家对Swarm有一个初步的认识，比如Swarm的具体工作流程：Docker Client发送请求给Swarm；Swarm处理请求并发送至相应的Docker Node；Docker Node执行相应的操作并返回响应。除此之外，Swarm的工作原理依然还不够明了。

深入理解Swarm的工作原理，可以先从Swarm提供的命令入手。Swarm支持的命令主要有4个：swarm create、swarm manage、swarm join、swarm list。当然还有一个swarm help命令，该命令用于指导大家如何正确使用swarm命令，本文不再赘述。

### 3.1 swarm create

Swarm中swarm create命令用于创建一个集群标志，用于Swarm管理Docker集群时，Docker Node的节点发现功能。

发起该命令之后，Swarm会前往Docker Hub上内建的发现服务中获取一个全球唯一的token，用以唯一的标识Swarm管理的Docker集群。

注：Swarm的运行需要使用服务发现，目前该服务内建与Docker Hub，该服务发现机制目前还在alpha版本，站点为：[http://discovery-stage.hub/docker.com](https://link.jianshu.com?t=http://discovery-stage.hub/docker.com) 。

### 3.2 swarm manage

Swarm中swarm manage是最为重要的管理命令。一旦swarm manage命令在Swarm节点上被触发，则说明用户需要swarm开始管理Docker集群。从运行流程的角度来讲，swarm经历的阶段主要有两点：启动swarm、接收并处理Docker集群管理请求。

Swarm启动的过程包含三个步骤：

- 发现Docker集群中的各个节点，收集节点状态、角色信息，并监视节点状态的变化；
- 初始化内部调度（scheduler）模块；
- 创建并启动API监听服务模块；

**第一个步骤**，Swarm发现Docker集群中的节点。发现（discovery）是Swarm中用于维护Docker集群状态的机制。既然涉及到发现（discovery），那在这之前必须先有注册（register）。Swarm中有专门负责发现（discovery）的模块，而关于注册（register）部分，不同的discovery模式下，注册（register）也会有不同的形式。

目前，Swarm中提供了5种不同的发现（discovery）机制：Node Discovery、File Discovery、Consul Discovery、EtcD Discovery和Zookeeper Discovery。

**第二个步骤**，Swarm内部的调度（scheduler）模块被初始化。swarm通过发现机制发现所有注册的Docker Node，并收集到所有Docker Node的状态以及具体信息。此后，一旦Swarm接收到具体的Docker管理请求，Swarm需要对请求进行处理，并通过所有Docker Node的状态以及具体信息，来筛选（filter）决策到底哪些Docker Node满足要求，并通过一定的策略（strategy）将请求转发至具体的一个Docker Node。

**第三个步骤**，Swarm创建并初始化API监听服务模块。从功能的角度来讲，可以将该模块抽象为Swarm Server。需要说明的是：虽然Swarm Server完全兼容Docker的API，但是有不少Docker的命令目前是不支持的，毕竟管理Docker集群与管理单独的Docker会有一些区别。当Swarm Server被初始化并完成监听之后，用户即可以通过Docker Client向Swarm发送Docker集群的管理请求。

Swarm的swarm manage接收并处理Docker集群的管理请求，即是Swarm内部多个模块协同合作的结果。请求入口为Swarm Server，处理引擎为Scheduler，节点信息依靠Disocovery。

### 3.3 swarm join

Swarm的swarm join命令用于将Docker Node添加至Swarm管理的Docker集群中。从这点也可以看出swarm join命令的执行位于Docker Node，因此在Docker Node上运行该命令，首先需要在Docker Node上安装Swarm，由于该Swarm只会执行swarm join命令，故可以将其当成Docker Node上用于注册的agent模块。

功能而言，swarm join可以认为是完成Docker Node在Swarm节点处的注册（register）工作，以便Swarm在执行swarm manage时可以发现该Docker Node。然而，上文提及的5种discovery模式中，并非每种模式都支持swarm join命令。不支持的discovery的模式有Node Discovery与File Discovery。

Docker Node上swarm join执行之后，标志着Docker Node向Swarm注册，请求加入Swarm管理的Docker集群中。Swarm通过注册信息，发现Docker Node，并获取Docker Node的状态以及具体信息，以便处理Docker请求时作为调度依据。

### 3.4 swarm list

Swarm中的swarm list命令用以列举Docker集群中的Docker Node。

Docker Node的信息均来源于Swarm节点上注册的Docker Node。而一个Docker Node在Swarm节点上注册，仅仅是注册了Docker Node的IP地址以及Docker监听的端口号。

使用swarm list命令时，需要指定discovery的类型，类型包括：token、etcd、file、zk以及<ip>。而swarm list并未罗列Docker集群的动态信息，比如Docker Node真实的运行状态，或者Docker Node在Docker集群中扮演的角色信息。

## 4.总结

Swarm的架构以及命令并没有很复杂，同时也为希望管理Docker集群的Docker爱好者降低了学习和使用门槛。

俗话说得好，没有一种一劳永逸的工具，有效的管理Docker集群同样也是如此。缺乏场景来谈论Swarm的价值，意义并不会很大。相反，探索和挖掘Swarm的特点与功能，并为Docker集群的管理提供一种可选的方案，是Docker爱好者更应该参与的事。

作者：garyond

链接：https://www.jianshu.com/p/298449db186f

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。