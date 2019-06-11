# [使用Kubernetes-Jenkins实现CI/CD](https://www.kubernetes.org.cn/1791.html)

2017-03-20 21:53 [中文社区](https://www.kubernetes.org.cn/author/edit) 分类：[Kubernetes实践分享/开发实战](https://www.kubernetes.org.cn/practice) 阅读(26523)	译者：容器时代 大伟	评论(1) 

> Author: Ramit Surana DevOps Zone
> DevOps Zone让你成为Sonatype Nexus的合作伙伴，Nexus套件能帮助你扩展DevOps交付过程，持续的将组件智能的集成到开发工具中，包括：Eclipse, IntelliJ, Jenkins, Bamboo, SonarQube等等，请看[演示](https://www.sonatype.com/nexus-lifecycle?utm_source=DZONE - Nexus Lifecycle - September 2016&utm_medium=DZONE - Nexus Lifecycle - September 2016&utm_campaign=DZONE - Nexus Lifecycle - September 2016)

关于持续集成和持续发布，[Martin Fowler](http://www.martinfowler.com/)给出了最好的定义：

> “持续集成是一种软件开发实践，团队成员可以频繁的集成他们的工作，通常每个人一天至少一次集成甚至多次集成。每次集成都通过自动化构建和测试进行验证，以尽快检测集成错误。许多团队发现，这种方法可以显著减少集成的问题，并允许团队更加快速的开发。”

## 简介

本文将讨论和探索两个令人惊奇和相当有趣的技术。一个是[Jenkins](https://www.kubernetes.org.cn/tags/jenkins)，一个流行的持续集成/发布的工具，另一个是Kubernetes，一个流行的容器编排引擎。另外一个惊喜，我们发现了Fabric8——一个酷炫的微服务平台。现在，让我们开始吧！

警告:在下文的几个步骤中，你的服务器可能会中途挂起几次，请选择配置高的PC。

## 方法论

有很多方法，可以让我们实现[CI/CD](https://www.kubernetes.org.cn/tags/cicd)，在本文中，我们重点介绍Kubneretes-Jenkins插件和Fabric8。

## 总体架构

在开始我们的工作之前，让我们花一点时间分析开始使用Jenkins使用Kubernetes容器所需的工作流。Kubernetes对于开发者来说是一个惊人的开源容器编排引擎。Kubernetes是由Google发起的，这使Kubernetes在使用多个开源容器项目方面有一个惊人的优势。默认情况下，Docker更受Kubernetes的使用者支持和青睐。使用Docker容器的工作流程如下图所示：

![20170320212433](https://www.kubernetes.org.cn/img/2017/03/20170320212433.jpg)

与使用rkt容器(rktnetes)类似，如下图：

![20170320212443](https://www.kubernetes.org.cn/img/2017/03/20170320212443.jpg)

目前，Jenkins还没有支持RKT容器的插件，但我认为工作流在其实现后也将保持不变。

## Kubernetes-Jenkins插件

在你的主机上安装Kubernetes

在主机上安装Kubernetes是一个容易的任务。如果你想在本地机器上试用它，我建议你试试Minikube，这里有快速安装指南：

1. 确认你的kubectl已经安装完成，参考[文档](https://www.kubernetes.org.cn/1417.html)
2. 确认已经下载完依赖的组件，参考先决条件
3. 下载、安装Minikube

Carlossg在使用Jenkins和Kubernetes的方面做了惊人的工作，他为Jenkins创建了一个特棒Kubernetes插件，使用这个插件，你可以很容易地直接使用Kubernetes。此外，还为用户提供了更容易的配置选项，他已经构建了一个包含Kubernetes插件的Jenkins镜像，镜像可以在Docker Hub上找到。在接下来的步骤中，我们将从Docker Hub上获取此镜像，并创建一个卷/var/jenkins/_home用于存储Jenkins的数据。

### 存在一个问题

虽然我们正在做我们计划做的一切，我们仍然会遇到一个问题。 你会注意到，每当你要关闭它后，重新启动你的Jenkins容器，所有的数据都会丢失。 无论你做了什么，例如创建作业，安装插件等等，都会丢失。 这是容器的常见问题之一。 让我们更深入地讨论它。

### 关于数据容器的一个词

当谈到容器时，数据是一个棘手的概念。在所有时间保证数据安全、可用性方面容器并不是一个很好的例子。过去发生了许多事件，其中已经看到容器会丢失数据。有很多种办法能解决这个问题，其中之一是使用Docker卷，我没有发现使用持久卷的好处。另一个办法是创建“数据容器”，并将其用作存储数据的源，而不是仅仅依赖一个镜像。
下图简单说明了我们使用“数据容器”来确保我们数据可用性的方法：

![20170320212452](https://www.kubernetes.org.cn/img/2017/03/20170320212452.jpg)

接下了的步骤是启动Jenkins Kubernetes容器：

```
// Running jenkins using another container containing data
 $ docker run --volumes-from jenkins-k8s -p 8080:8080 -p 50000:50000 \
 -v /var/jenkins_home csanchez/jenkins-kubernetes

// Created a container for containing jenkins data with
 // the image name csanchez/jenkins-kubernetes
 $ docker create --name jenkins-k8s csanchez/jenkins-kubernetes
```

打开浏览器输入http://localhost:8080，你会看到如下界面：

![20170320212459](https://www.kubernetes.org.cn/img/2017/03/20170320212459.jpg)

### Jenkins的Kubernetes插件设置

现在，Jenkins已经预先配置好了Kubernetes插件，所以我们直接跳到下一步。使用Jenkins GUI，使用Manage Jenkins > Configure System > Cloud > Add a new Cloud > Kubernetes。界面如下图中的几个步骤：

![20170320212509](https://www.kubernetes.org.cn/img/2017/03/20170320212509.jpg)

接下来按照下图的设置进行配置：

![20170320212516](https://www.kubernetes.org.cn/img/2017/03/20170320212516.jpg)

如果你想使用Jenkins slave，可以在Docker hub上下载jnlp-slave镜像。它提供了简单安装Slave节点的模板。你可以通过创建模板来配置一个Slave节点，如下图所示：

![20170320212524](https://www.kubernetes.org.cn/img/2017/03/20170320212524.jpg)

为了让Jenkins slave能参与任务调度，当在Jenkins上创建一个任务的时候，向下图所示设置你的任务：

![20170320212532](https://www.kubernetes.org.cn/img/2017/03/20170320212532.jpg)

现在只需把Kubernetes Pod模板中的标签的名称放在restrict部分，保存并应用新的设置。当构建此Job时，会看到Slave上运行这个Job。
一切准备就绪了！你现在可以根据需要添加更多的插件。

## Fabric8

Fabric8是一个基于Docker，Kubernetes和Jenkins的开源微服务平台。 它是由Red Hat创建的。 该项目的目的是通过持续交流水线轻松创建，构建，测试和部署微服务，然后使用持续改进和ChatOps运行和管理它们。
Fabric8会自动安装并配置一下内容：

- Jenkins
- Gogs
- Fabric8 registry
- Nexus
- SonarQube

下图是Fabric8的架构图：

![20170320212843](https://www.kubernetes.org.cn/img/2017/03/20170320212843.jpg)

为了开始我们的演示，你需要使用命令行工具安装Fabric8(gofabric8)。下载gofabric8，解压之后运行命令：

```
$ sudo cp /usr/local/bin/ gofabric8
```

在终端上检查$ gofabric8命令是否安装成功：

```
$ gofabric8 deploy -y
```

运行命令后，终端上会显示：

![20170320212850](https://www.kubernetes.org.cn/img/2017/03/20170320212850.jpg)

创建秘钥：

```
$ gofabric8 secrets -y
```

终端返回：

![20170320212900](https://www.kubernetes.org.cn/img/2017/03/20170320212900.jpg)

使用kubectl查看pod运行状态:

![20170320212907](https://www.kubernetes.org.cn/img/2017/03/20170320212907.jpg)

你可以使用Kubernetes Dashboard提供的页面查看所有Pod的状态，打开浏览器，输入：http://192.168.99.100:30000:

![20170320212915](https://www.kubernetes.org.cn/img/2017/03/20170320212915.jpg)

相似的，可以打开Fabric8的页面：

![20170320212922](https://www.kubernetes.org.cn/img/2017/03/20170320212922.jpg)

我们来分析一下上面的命令的执行过程，可以通过一个工作流图展示：

![20170320212929](https://www.kubernetes.org.cn/img/2017/03/20170320212929.jpg)

## 实现CI/CD

说起来容易做起来难。从源头构建Jenkins并整合Kubernetes实现持续集成(CI)仅仅是故事的一部分，但是实现持续发布(CD)时另外一个非常不同而且更加复杂的故事了。
这里有一些关于使用Jenkins插件的技巧，他们能帮你更加容易地实现Jenkins的持续交付。

### Pipeline Plugin

Pipeline是由Jenkins社区构建的核心插件。此插件确保任何编排引擎与你的环境集成，而且复杂性很低。目前，我相信这仅仅是个开始，因为不同的社区已经为这种引擎构建不同的插件，这些插件都围绕Jenkins UI展开。使用Pipeline插件，用户可以在Jenkinsfile中实现他们项目的整个构建/测试/部署的流水线，并将这个文件跟代码存储在一起，作为代码的一部分放进代码控制中。

### GitHub Plugin

这些天，大多数工作都使用GitHub作为源代码管理(SCM)工具。我建议你使用GitHub插件，它可以帮助你的Jenkins从GitHub拉取代码，并分析和测试。为了实现鉴权访问，我建议你看看GitHub OAuth插件。

### Docker Plugin

对于Docker来说，这是最适合的插件之一，帮助你做几乎一切与Docker有关的事情。 这个插件还能帮助你使用Docker容器作为Jenkins Slave节点。还有几个其他的Docker插件，根据时间和你的用法，可以在它们之间切换。

### AWS Plugin

AWS人员推出了一个名为AWS Pipeline的超棒的服务。 此特定服务可帮助您使用AWS实现持续交付。 目前，这个插件正在大量开发，可能不适合生产环境。 另外，可以查看AWS CodeCommit关注进度。

### OpenStack

对于OpenStack用户，OpenStack插件适合使用OpenStack的环境配置。

### Google Cloud Platform

可以在Google Cloud Platform上提供了部署管理器，使用部署管理器，你可以创建灵活的声明性模板，这些模板可以部署各种云平台，例如Google Cloud Storage, Google Compute Engine和Google Cloud SQL。部署管理器还可以将资源的使用定义存储在发布模板中。这是一个非常新的插件，但是我认为他是一个值得尝试好工具，如果你希望实现自动化和Google的云服务。