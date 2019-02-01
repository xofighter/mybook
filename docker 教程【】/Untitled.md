# 一、Swarm简介
在Docker的官方文档当中，我们可以看到在Docker 1.12及更高版本中，Swarm模式与Docker Engine集成。那么Dokcer Swarm到底是个什么？

Docker Swarm是Docker官方的三剑客项目之一，提供Docker容器集群服务，是Docker官方对容器云生态镜像支持的核心方案。它是Docker公司推出的官方容器集群平台，基于Go语言实现，代码在https://github.com/docker/swarm。

Docker Swarm是原生支持Docker集群管理的工具。它可以把多个Docker主机组成的系统转换为单一的虚拟Docker主机，使得容器可以组成跨主机的子网网络。

在很多台机器上部署Docker，组成一个Docker集群，并把整个集群的资源抽象成资源池，使用者部署Docker应用的时候，只需要将应用交给Swarm，Swarm会根据整个集群资源的使用情况来分配资源给部署的Docker应用，可以将这个集群的资源利用率达到最大。

使用Docker CLI创建群集，将应用程序服务部署到群集，并管理群体行为。其主要的目的就是更好的帮助用户管理多个Docker Engine，方便用户使用，像使用Docker Engine一样使用容器集群服务。

# 二、关于集群
1、关于集群
前面的简介里，我们提到了集群的概念。那么这里我们来说一下，什么是集群？

　集群是一组协同工作的服务实体（可理解为服务器），用以提供比单一服务实体更具扩展性与可用性的服务平台。在客户端看来，一个集群就像是一个服务实体，但事实上集群由一组服务实体组成。与单一服务实体相比较，集群提供了以下两个关键特性：

可扩展性——集群的性能不限于单一的服务实体，新的服务实体可以动态地加入到集群，从而增强集群的性能。

高可用性——集群通过服务实体冗余使客户端免于轻易遇到out of service的警告。在集群中，同样的服务可以由多个服务实体提供。如果一个服务实体失败了，另一个服务实体会接管失败的服务实体。集群提供的从一个出错的服务实体恢复到另一个服务实体的功能增强了应用的可用性。

2、Docker集群服务概念：
在深入学习之前，我们需要明确几个概念：

- Swarm
Swarm运行 Docker Engine 的多个主机组成的集群。

从 v1.12 开始，集群管理和编排功能已经集成进 Docker Engine。当 Docker Engine 初始化了一个Swarm或者加入到一个存在的Swarm时，它就启动了 Swarm Mode。

没启动Swarm Mode时，Docker执行的是容器命令；运行Swarm Mode后，Docker增加了编排service的能力。

Docker允许在同一个Docker主机上既运行Swarm Service，又运行单独的容器。

- node
Swarm中的每个Docker Engine都是一个node，有两种类型的 node：manager 和worker。

为了向Swarm中部署应用，我们需要在manager node上执行部署命令，manager node会将部署任务拆解并分配给一个或多个worker node完成部署。

manager node负责执行编排和集群管理工作，保持并维护Swarm处于期望的状态。Swarm中如果有多个manager node，它们会自动协商并选举出一个leader 执行编排任务。

woker node接受并执行由manager node派发的任务。默认配置下manager node同时也是一个worker node，不过可以将其配置成manager-only node，让其专职负责编排和集群管理工作。

work node会定期向manager node报告自己的状态和它正在执行的任务的状态，这样manager就可以维护整个集群的状态。

- service
service定义了worker node上要执行的任务。swarm的主要编排任务就是保证 service处于期望的状态下。

举一个service的例子：在swarm中启动一个nginx服务，使用的镜像是 nginx:latest，副本数为3。

manager node负责创建这service，经过分析知道需要启动3个nginx容器，根据当前各worker node的状态将运行容器的任务分配下去，比如worker1上运行两个容器，worker2上运行一个容器。

运行了一段时间，worker2突然宕机了，manager监控到这个故障，于是立即在 worker3上启动了一个新的nginx容器。

这样就保证了service处于期望的三个副本状态。

总结一下： 
swarm以节点（node）的方式组织集群（cluster）；同时每个节点上面可以部署一个或者多个服务（service），每个服务又可以包括一个或者多个容器（container）。

Swarm的架构图如下： 


# 三、Swarm实战
因为现在新版本的Docker已经结合了Swarm，所以在这里主要结合的是新版本功能的Docker的演示。

1、Swarm基本命令
docker swarm init 创建Swarm，同时让swarm-manager 成为 manager node。 
执行该命令后，显示添加worker node和manager node 需要执行的命令。 
–advertise-addr 指定与其他node通信的地址。

docker swarm init --advertise-addr [ip]
1
查看swarm worker的连接令牌

docker swarm join-token worker
1
查看swarm manager的连接令牌

docker swarm join-token manager
1
加入docker swarm集群，这里的token和ip更换为具体需求下的即可。 
如果忘记可以用docker swarm join-token worker查看。

docker swarm join --token SWMTKN-1-1next4gw4m7yx3j6q1f4z4ooimvp3pc1ahsnnwifu5w1h6b247-f10wphfpnyyunzphr1qp4kew1 192.168.1.245:2377
1
查看集群中的节点

docker node ls
1
将节点升级为manager

docker node promote work-node1
1
将节点降级为worker

docker node demote work-node1
1
只能删除down状态的节点，如何要删除active状态的，需要加上–force

docker node rm --force worker-manager
1
查看服务列表

docker service ls
1
查看具体服务信息，这里是web服务

docker service ps web
1
创建一个名称为web，副本为3，开放端口为80的nginx服务

docker service create --name web --replicas 3 -p 80:80 nginx
1
删除一个服务

docker service rm web
1
查看集群中节点信息

docker node inspect work-node1 --pretty
1
查看服务的详细信息

docker service inspect --pretty <SERVICE-ID>/<SERVICE-NAME>
1
调度程序不会将新任务分配给节点。 
调度程序关闭任何现有任务并在可用节点上安排它们。

docker node update --availability drain work-node1
1
调度程序可以将任务分配给节点

docker node update --availability active work-node1
1
调度程序不向节点分配新任务，但是现有任务仍然保持运行

docker node update --availability pause work-node1
1
为指定的服务删除一个开放端口

docker service update --publish-rm 8080:80 web
1
为指定的服务添加一个开放端口

docker service update --publish-add 8080:80 web
1
回滚至之前版本

docker service update --rollback mysql
1
扩展或缩放服务中容器的数量

docker service scale web=5
1
以上是一些常用的docker swarm命令，接下来我们就进入实战。

2、Swarm实战
A.创建基本的集群环境
接下来会创建三个节点的Swarm集群。如下图：



swarm-manager 是 manager node，swarm-worker1 和 swarm-worker2 是 worker node，可以认为是worker-node1、worker-node2节点。 
swarm-manager、swarm-worker1和swarm-worker2均为Linux系统服务器，系统为Ubuntu 16.04。 
先在swarm-manager使用docker swarm init命令

docker swarm init --advertise-addr 39.108.119.87
1
这里的ip为具体本机服务ip。 
初始化为swarm-manager节点，如下：



使用docker node ls 我们可以查看到创建的节点： 

然后我们在swarm-worker1、和swarm-worker2服务器下面分别输入刚才初始化成功后的提示消息，比如在swarm-worker2节点下输入下面命令：

docker swarm join --token SWMTKN-1-1nkutg1v1bwjvdsy10xgocw1hftusf51xomjj735fd9d08s0ju-5pizwmfjtttcpvaht8vsnv3s1 39.108.119.87:2377
1
我们可以看到提示： 


This node joined a swarm as a worker. 提示我们添加节点成功。

当我们去swarm-manager节点进行查看时可以发现，出现了三个节点，其中的两个就是刚才我们添加的。



当然我们也可以删除某个节点，使用docker node rm , 这个只能删除down状态的节点，如何要删除active状态的。需要加上–force。

docker node rm --force sprkw7t8irlb4l3dtvwszpdjj
1

至此，三节点的swarm集群就已经搭建好了，操作还是相当简单的。

B.服务创建及伸缩
在上面我们搭建好了集群环境。现在我们在manager节点上部署一个基本的nginx服务，服务数量为（副本数）2个，公开指定端口是8080映射容器80，使用nginx镜像。

docker service create --replicas 2 --name nginx --publish 8080:80  nginx
1


通过docker service ls可以查看当前swarm中的服务。 

可以看到服务正在启动过程当中。

稍等一会我们再去查看服务，并使用docker service ps nginx查看具体的nginx服务，如下： 


可以发现，nginx服务已经启动起来了，并且在不同的节点里面运行起来了。

这里主要在swarm-manager和swarm-worker1里面分别启动了一个容器。

我们知道平时通常会运行多个实例。这样可以负载均衡，同时也能实现服务器高可用。

比如现在我们想把nginx服务数目提升到5个，提高可用性，就可以使用如下命令：

docker service scale nginx=5
1


我们可以查看到启动了5个nginx服务，并且分布在不同的节点，从中可以看到swarm-manager节点里启动了1个，swarm-worker1和swarm-worker2分别启动了2个节点。

我们可以去相应节点服务器，通过docker ps -a 查看，比如：swarm-manager节点，只有一个容器运行： 


swarm-worker1节点，有两个容器运行： 


swarm-worker2节点，有两个容器运行： 


具体结果如下： 


我们知道默认配置下manager node 也是 worker node，所以 swarm-manager 上也运行了副本。如果不希望在 manager 上运行 service，可以进行排空，就是将该节点上的容器排空。命令如下：

docker node update --availability drain [NODE-ID]
1
执行过后，我们再查看具体服务情况如下： 


我们可以看到在swarm-manager上的容器nginx状态是shutdown了，然后在swarm-worker2上启动了一个nginx服务。

查看节点状态，docker node ls可以发现manager上的状态为drain。



当然如果我们要减少服务数目，直接把scale的数据设置比当前启动的服务少即可。比如减少服务数到3个，如下命令：

docker service scale nginx=3
1
结果如下，shutdown为先前排除manager节点里的容器，可以看到只有三个运行的服务。 


最后集群服务情况如下： 


C.服务之间的通信
比如我们在创建多个服务之间要通信，某个服务不要求与外部通信（比如数据库等）。这里就用到了服务发现。

我们就可以使用建立overlay网络，部署 service 到 overlay网络上去。

比如新建dev网络环境：

docker network create --driver overlay dev
1
 
然后我们新建服务，就可以让服务部署在该网络上。比如下面我们新建nginx版本1.12.2的服务，副本为2，开放80端口，部署到我们新建的dev网络上。

docker service create --replicas 2 --name nginx-dev --publish 9090:80  --network dev nginx:1.12.2
1

我们可以看到服务正在启动，我们可以在不同的节点下看到dev网络有该容器，而相同网络下的服务就可以进行通信，ip直接是可以访问到的。

D.服务镜像升级和回滚
在前面我们部署了多个副本的服务，如果我们要升级某个服务该怎么办，如下，我们对刚刚dev网络环境下的nginx:1.12.2进行升级，升级到1.13.12版本。使用如下命令：

docker service update --image nginx:1.13.12 nginx-dev
1
可以看到服务状态，新版本运行起来了，旧版本被停止了。 


Swarm 将按照如下步骤执行滚动更新：

停止第一个副本。
调度任务，选择 worker node。
在 worker 上用新的镜像启动副本。
如果副本（容器）运行成功，继续更新下一个副本；如果失败，暂停整个更新过程。
默认配置下，Swarm 一次只更新一个副本，并且两个副本之间没有等待时间。我们可以通过 –update-parallelism 设置并行更新的副本数目，通过 –update-delay 指定滚动更新的间隔时间。

比如执行如下命令：

docker service update --replicas 6 --update-parallelism 2 --update-delay 1m30s nginx-dev
1
service 增加到六个副本，每次更新两个副本，间隔时间一分半钟。

Swarm 还有个回滚功能，如果更新后效果不理想，可以通过 –rollback 快速恢复到更新之前的状态。

docker service update --rollback nginx-dev
1


可以看到服务已经回滚到先前的版本。到此docker中swarm的简单使用就到此结束，后面再会介绍Docker三剑客的其他内容。

参考
《每天5分钟玩转Docker容器技术》
http://dockone.io/article/662
