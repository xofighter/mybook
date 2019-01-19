Docke实战
Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上。

Docker是一个重新定义了程序开发测试、交付和部署过程的开放平台，Docker则可以称为构建一次，到处运行，这就是docker提出的“Build once，Run anywhere”。 
简单来说，就是你需要一个Tomcat，然后你去Docker镜像网站下载一个，然后你就可以用Tomcat了！不需要你本地下载安装Tomcat。

Docker架构
Docker系统有两个程序：docker服务端和docker客户端。其中docker服务端是一个服务进程，管理着所有的容器。docker客户端则扮演着docker服务端的远程控制器，可以用来控制docker的服务端进程。大部分情况下，docker服务端和客户端运行在一台机器上。

![è¿éåå¾çæè¿°](http://dockerone.com/uploads/article/20141229/b28a5a6c1709746deb0aa2530be49af1.png)

Docker核心概念
镜像（Image）：类似于虚拟机镜像，可以将它理解为一个面向Docker引擎的只读模板，包含文件系统，镜像是创建Docker容器的基础。

容器（Container）：Docker容器类似于一个轻量级沙箱，Docker利用容器来运行和隔离应用，容器是从镜像的应用运行实例，可以将其启动、开始、停止和删除，而这些容器都是相互隔离、互不可见的。

仓库（Repository） : 代码仓库，是Docker集中存放镜像文件的场所，目前最大的公开仓库为Docker Hub

运行任何应用程序，都需要有两个基本步骤： 
第1步：构建镜像 
第2步：运行容器

Docker安装
Linux安装Docker
查看系统版本： 
$ cat /etc/redhat-release
[root@izuf64g8sd65ovaejq5rn0z ~]#CentOS Linux release 7.4.1708 (Core) 
1
安装docker 
$ yum install docker
检查安装是否成功 
$docker version
[root@izuf64g8sd65ovaejq5rn0z ~]# docker version
Client:
 Version:         1.12.6
 API version:     1.24
 Package version: docker-1.12.6-68.gitec8512b.el7.centos.x86_64
 Go version:      go1.8.3
 Git commit:      ec8512b/1.12.6
 Built:           Mon Dec 11 16:08:42 2017
 OS/Arch:         linux/amd64

Server:
 Version:         1.12.6
 API version:     1.24
 Package version: docker-1.12.6-68.gitec8512b.el7.centos.x86_64
 Go version:      go1.8.3
 Git commit:      ec8512b/1.12.6
 Built:           Mon Dec 11 16:08:42 2017
 OS/Arch:         linux/amd64

若输出了 Docker 的版本号，说明安装成功了

Docker命令
1. 镜像管理
  下载image
  下载镜像的命令非常简单，使用docker pull命令即可。(译者按：docker命令和git有一些类似的地方）。在docker的镜像索引网站上面，镜像都是按照用户名/镜像名的方式来存储的。有一组比较特殊的镜像，比如ubuntu这类基础镜像，经过官方的验证，值得信任，可以直接用镜像名来检索到

docker pull <IMAGE_ID>

列出本地主机上已有镜像
docker images

[root@izuf64g8sd65ovaejq5rn0z ~]# docker images
REPOSITORY                                         TAG                 IMAGE ID            CREATED             SIZE
docker.io/tomcat                                   latest              5e402de638b8        11 days ago         557.4 MB

在列出的信息中，可以看到几个字段信息：

1.来自于那个仓库 
2.镜像的标签信息 
3.镜像的ID号（唯一） 
4.5.镜像大小

搜索可用的镜像
docker search 镜像名

[root@izuf64g8sd65ovaejq5rn0z ~]# docker search mysql
INDEX       NAME                                                             DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/mysql                                                  MySQL is a widely used, open-source relati...   5586      [OK]       
docker.io   docker.io/mariadb 

可以看到返回了很多包含关键字的镜像，其中包括镜像名字、描述、星级、是否是官方创建、是否自动创建等。

删除image
docker rmi <IMGE_ID>
如果当镜像创建的容器存在时，镜像文件默认是无法删除的，例如

[root@izuf64g8sd65ovaejq5rn0z ~]# docker rmi 5e
Error response from daemon: conflict: unable to delete 5e402de638b8 (cannot be forced) - image is being used iner cbebb11fe092
这时可以有两种解决方法 
1.先删除容器，再删除镜像 
2.使用 -f 强制删除 
第二钟方法不推荐，会有问题的哦- 查找image

1.基于已有镜像的容器创建

docker commit [OPTIONS] <IMAGE_ID>
选项：

    - a ,--author-- "作者信息"
        - m, --mesage-- "提交信息"
        - p, --pause=true-- 提交时暂停容器运行

2.基于本地模板导入（OPENVZ），自己百度，这里不做介绍

存入和载出镜像
1.存出镜像

[root@izuf64g8sd65ovaejq5rn0z ~]# docker save -o busybox.tar busybox
[root@izuf64g8sd65ovaejq5rn0z ~]# ls
busybox.tar
2.载入镜像

[root@izuf64g8sd65ovaejq5rn0z ~]# docker load --input busybox.tar
Loaded image: docker.io/busybox:latest
上传镜像
docker  push NAME[:TAG]
2.容器管理
容器就是镜像运行的一个实例，所不同的是，它带有额外可读写层

新建并启动容器：
docker run -i -t <IMAGE_ID> /bin/bash：-i：标准输入给容器    -t：分配一个虚拟终端    /bin/bash：执行bash脚本
-d：以守护进程方式运行（后台）
-P：默认匹配docker容器的5000端口号到宿主机的49153 to 65535端口
-p <HOT_PORT>:<CONTAINER_PORT>：指定端口号
- -name： 指定容
  器的名称
- -rm：退出时删除容器
  停止container
  docker  stop  <CONTAINER_ID>
  重新启动container
  docker start <CONTAINER_ID>
  显示存在的容器
  docker ps -l/-a
  -l：显示最后启动的容器
  -a：同时显示停止的容器，默认只显示启动状态
  连接到启动的容器
  docker attach <CONTAINER_ID> 
  输出容器日志
  docker logs <CONTAINER_ID> 
  复制容器内的文件到宿主机目录上
  docker cp <CONTAINER_ID>:path hostpath
  进入容器
  [root@izuf64g8sd65ovaejq5rn0z ~]# docker exec -ti cbeb /bin/bash
  root@cbebb11fe092:/usr/local/tomcat# ls
  LICENSE  RELEASE-NOTES  bin   include  logs        temp work
  NOTICE   RUNNING.txt    conf  lib      native-jni-lib  webapps
  删除容器
  docker rm  [OPTIONS]  CONTAINER
  选项：

    - f,--force=false 强行终止并删除一个运行中的容器
    - l,--link=false 删除容器连接，但保留容器
    - v,--volumes=false 删除容器挂在的数据卷
    容器的导入和导出
    导出：
    [root@izuf64g8sd65ovaejq5rn0z ~]# docker export 8d >nginx.tar
    [root@izuf64g8sd65ovaejq5rn0z ~]# ls
    busybox.tar  nginx.tar
    导入：有问题

    3.仓库管理
    这里介绍阿里云开源仓库，比较快 
    https://dev.aliyun.com/search.html

使用Registry镜像创建私有仓库（后续跟进）

参考：Docker技术入门与实战
--------------------- 
作者：Adam_allen 
来源：CSDN 
原文：https://blog.csdn.net/adam_allen/article/details/79129691 
版权声明：本文为博主原创文章，转载请附上博文链接！