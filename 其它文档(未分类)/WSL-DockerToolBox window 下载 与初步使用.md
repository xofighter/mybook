# DockerToolBox window 下载 与初步使用

2018年05月18日 09:38:21 [好命靠努力](https://me.csdn.net/qq_35568099) 阅读数：7303



 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/qq_35568099/article/details/80359824

1.dockerToolBox下载

下载路径1:http://mirrors.aliyun.com/docker-toolbox/windows/docker-toolbox/

![img](https://img-blog.csdn.net/20180518093657391?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTY4MDk5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)





下载路径2:

https://docs.docker.com/toolbox/toolbox_install_windows/

![img](https://img-blog.csdn.net/20180518093725127?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTY4MDk5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)







[![image](https://www.linuxidc.com/upload/2016_07/160725065623202.png)](https://www.linuxidc.com/upload/2016_07/160725065623201.png)

## 1.Docker溯源

　　Docker的前身是名为dotCloud的小公司，主要提供的是基于 PaaS（Platform as a Service，平台及服务）平台为开发者或开发商提供技术服务，并提供的开发工具和技术框架。因为其为初创的公司，又生于IT行业，dotCloud受到了IBM，亚马逊，google等公司的挤压，发展举步维艰。于是，在2013年dotCloud 的创始人，年仅28岁的Solomon Hykes做了一个艰难的决定：将dotCloud的核心引擎开源！然而一旦这个基于 LXC（Linux Container）技术的核心管理引擎开源，dotCloud公司就相当于走上了一条"不归路"。可正是这个孤注一掷的举动，却带来了全球技术人员的热潮，众程序员惊呼：太方便了，太方便了。也正是这个决定，让所有的IT巨头也为之一颤。一个新的公司也随之出世，它就是：Docker。可以说，Docker是一夜成名的！！

## 2.Docker认识

### 2.1镜像，容器，仓库

　　首先，需要了解一下几个概念：镜像，容器，仓库

> **镜像（image）：**Docker 镜像就是一个只读的模板，镜像可以用来创建 Docker 容器。Docker 提供了一个很简单的机制来创建镜像或者更新现有的镜像，用户甚至可以直接从其他人那里下载一个已经做好的镜像来直接使用。镜像是一种文件结构。Dockerfile中的每条命令都会在文件系统中创建一个新的层次结构，文件系统在这些层次上构建起来，镜像就构建于这些联合的文件系统之上。Docker官方网站专门有一个页面来存储所有可用的镜像，网址是：[index.docker.io](http://index.docker.io/)。

> **容器（ Container）**：容器是从镜像创建的运行实例。它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台。可以把容器看做是一个简易版的 Linux 环境，Docker 利用容器来运行应用。

> **仓库**：仓库是集中存放镜像文件的场所，仓库注册服务器（Registry）上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签（tag）。目前，最大的公开仓库是 Docker Hub，存放了数量庞大的镜像供用户下载。

### 2.2Docker定义

> Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上。Docker是一个重新定义了程序开发测试、交付和部署过程的开放平台，Docker则可以称为构建一次，到处运行，这就是Docker提出的"**Build once，Run anywhere**"

　　Docker仓库用来保存我们的images，当我们创建了自己的image之后我们就可以使用push命令将它上传到公有或者私有仓库，这样下次要在另外一台机器上使用这个image时候，只需要从仓库上pull下来就可以了。注意：Docker不是容器，而是管理容器的引擎！

> Docker中文手册上解释说：Docker是一个开源的引擎，可以轻松的为任何应用创建一个轻量级的、可移植的、自给自足的容器。开发者在笔记本上编译测试通过的容器可以批量地在生产环境中部署，包括VMs（虚拟机）、bare metal、OpenStack 集群和其他的基础应用平台。

　　从这里我们可以看出，Docker并非是容器，而是管理容器的引擎。Docker是为应用打包、部署的平台，而非单纯的虚拟化技术。

## 3.Docker安装

### 3.1下载和安装Docker Toolbox

　　从Docker官网可以下到Docker Toolbox：<https://www.docker.com/products/docker-toolbox>，选择windows版本下载。

[![image](https://www.linuxidc.com/upload/2016_07/160725065623204.png)](https://www.linuxidc.com/upload/2016_07/160725065623203.png)

　　双击下载好的程序：

注意：安装路径千万不要有中文！！！！

[![image](https://www.linuxidc.com/upload/2016_07/160725065623206.png)](https://www.linuxidc.com/upload/2016_07/160725065623205.png)

[![image](https://www.linuxidc.com/upload/2016_07/160725065623208.png)](https://www.linuxidc.com/upload/2016_07/160725065623207.png)

请注意下面一点，对最后一项打上钩：

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232010.png)](https://www.linuxidc.com/upload/2016_07/160725065623209.png)

然后，点击Install！

### 3.2Docker配置

安装完成后，在桌面上会出现两个图标：一个是命令行形式Docker终端，一个是图形界面的Docker操作工具。

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232012.png)](https://www.linuxidc.com/upload/2016_07/1607250656232011.png)

其安装路径下文件如下：

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232014.png)](https://www.linuxidc.com/upload/2016_07/1607250656232013.png)

你可通过CMD查看docker是否安装成功，输入docker-machine，出现版本等信息，安装能够运行，还会给出一些选项，包括machine安装路劲等： -s, --storage-path "C:\Users\wcc335836929\.docker\machine"    Configures storage path [$MACHINE_STORAGE_PATH]

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232016.png)](https://www.linuxidc.com/upload/2016_07/1607250656232015.png)

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232046.png)](https://www.linuxidc.com/upload/2016_07/1607250656232045.png)

在使用Docker前，要先创建docker machine，它其实是台虚拟机，不过在使用它的时候，你可能根本察觉不出来它是虚拟的。默认情况下，docker machine的所有文件都会保存在用户的根目录下，一般是在C盘，如果就用默认的位置，C盘的空间会越来越小，所以最好还是换个地方。 点击Git Bash，在起开所有docker应用之前需要先使用这个才能更改成功，修改默认的default machine的路径，不然，默认安装在C盘，会使C盘空间越来越小。

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232018.png)](https://www.linuxidc.com/upload/2016_07/1607250656232017.png)

然后，输入 notepad .bash_profile 创建和打开.bash_profile 配置文件

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232020.png)](https://www.linuxidc.com/upload/2016_07/1607250656232019.png)

然后，在空白处输入（因为我想将以后的镜像都安装到H盘，此处可以修改你喜欢的盘符）：

```
export MACHINE_STORAGE_PATH='H:\docker'
```

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232022.png)](https://www.linuxidc.com/upload/2016_07/1607250656232021.png)

然后，关闭后，在H盘创建名为docker的文件夹，在其下创建名为cache的文件夹，将安装文件下的boot2docker.iso拷贝到该文件夹：

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232024.png)](https://www.linuxidc.com/upload/2016_07/1607250656232023.png)

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232026.png)](https://www.linuxidc.com/upload/2016_07/1607250656232025.png)

在Git Bash中输入更改路径命令，此处使用阿里云的加速器，因为在使用docker的时候，会需要从docker的网站下载镜像文件，下载速度可能会很慢。获得阿里云加速，需���登录阿里云开发者平台，然后点击右侧的管理中心（当然你得要有一个账户）：

阿里云开发者平台：<https://dev.aliyun.com/search.html>

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232048.png)](https://www.linuxidc.com/upload/2016_07/1607250656232047.png)

然后点击加速，Windows

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232050.png)](https://www.linuxidc.com/upload/2016_07/1607250656232049.png)

复制红色方框中的代码，然后修改为如下（根据自己盘符修改）：

```
docker-machine -s "H:\docker" create --engine-registry-mirror=https://vf29u5xi.mirror.aliyuncs.com -d virtualbox default
```

现在就可以在Git Bash中运行了：

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232028.png)](https://www.linuxidc.com/upload/2016_07/1607250656232027.png)

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232030.png)](https://www.linuxidc.com/upload/2016_07/1607250656232029.png)

虚拟机中出现如下界面：

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232032.png)](https://www.linuxidc.com/upload/2016_07/1607250656232031.png)  此时可以点击Docker Quickstart Terminal：

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232034.png)](https://www.linuxidc.com/upload/2016_07/1607250656232033.png)

同时，Docker Quickstart Terminal也已经登录进去：

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232036.png)](https://www.linuxidc.com/upload/2016_07/1607250656232035.png)

也可以在Git Bash中运行：

```
docker-machine ls 
```

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232038.png)](https://www.linuxidc.com/upload/2016_07/1607250656232037.png)

这里面就是一个linux风格的Docker环境，提示的IP：192.168.99.100就是Docker虚拟机的IP了。

这台docker machine创建后就启动了，如果下次我们再打开电脑，想要启动docker machine，可以打开Docker Quickstart Terminal或者Git Bash，然后执行下面的命令：

```
docker-machine start
```

来启动docker machine。

### 3.3利用ssh软件登录

SSH登录虚拟机（此处使用的XShell软件，自行百度下载，当然还有其他的ssh软件）：

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232040.png)](https://www.linuxidc.com/upload/2016_07/1607250656232039.png)

docker machine的IP是192.168.99.100，用SSH工具登录它，用户名是`docker`，密码是`tcuser`

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232042.png)](https://www.linuxidc.com/upload/2016_07/1607250656232041.png)

[![image](https://www.linuxidc.com/upload/2016_07/1607250656232044.png)](https://www.linuxidc.com/upload/2016_07/1607250656232043.png)

**更多Docker相关教程见以下内容**： 

Docker安装应用([CentOS](https://www.linuxidc.com/topicnews.aspx?tid=14) 6.5_x64) [http://www.linuxidc.com/Linux/2014-07/104595.htm](https://www.linuxidc.com/Linux/2014-07/104595.htm) 

[Ubuntu](https://www.linuxidc.com/topicnews.aspx?tid=2) 14.04安装Docker  [http://www.linuxidc.com/linux/2014-08/105656.htm](https://www.linuxidc.com/linux/2014-08/105656.htm) 

Ubuntu使用VNC运行基于Docker的桌面系统  [http://www.linuxidc.com/Linux/2015-08/121170.htm](https://www.linuxidc.com/Linux/2015-08/121170.htm) 

阿里云CentOS 6.5 模板上安装 Docker [http://www.linuxidc.com/Linux/2014-11/109107.htm](https://www.linuxidc.com/Linux/2014-11/109107.htm) 

Ubuntu 15.04下安装Docker  [http://www.linuxidc.com/Linux/2015-07/120444.htm](https://www.linuxidc.com/Linux/2015-07/120444.htm) 

在Ubuntu Trusty 14.04 (LTS) (64-bit)安装Docker [http://www.linuxidc.com/Linux/2014-10/108184.htm](https://www.linuxidc.com/Linux/2014-10/108184.htm) 

在 Ubuntu 15.04 上如何安装Docker及基本用法 [http://www.linuxidc.com/Linux/2015-09/122885.htm](https://www.linuxidc.com/Linux/2015-09/122885.htm) 

**Docker 的详细介绍**：[请点这里](https://www.linuxidc.com/Linux/2013-10/91050.htm)
**Docker 的下载地址**：[请点这里](https://www.linuxidc.com/down.aspx?id=1020)

**本文永久更新链接地址**：[http://www.linuxidc.com/Linux/2016-07/133506.htm](https://www.linuxidc.com/Linux/2016-07/133506.htm)