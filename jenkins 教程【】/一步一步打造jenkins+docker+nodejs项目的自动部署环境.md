# 一步一步打造jenkins+docker+nodejs项目的自动部署环境

## 介绍

jenkins+docker+nodejs 自动部署

## 亮点

- 采用shell自定义脚本，控制集成部署环境更加方便灵活
- 如果服务器更换，重新部署的工作量会比较小
- 涉及的docker命令，会有讲解（不知道算不算亮点）

## 前提

> 1. 代码通过git管理，放在了[码云](https://link.jianshu.com?t=https://git.oschina.net/)上（放在github或者其他平台也都可以）
> 2. 服务器用的亚马逊aws（阿里云，青云也一样）
> 3. 服务器用的ubuntu 16.04 (服务器系统不同，只是某些shell命令不同而已，windows服务器用户，呃，我敬你是条汉子)
> 4. 客户端用的mac
> 5. docker镜像平台为dockerhub

## 准备

1. 服务器开通SSH
2. 服务器安装Docker
3. 掌握基本的shell命令（本文出现的shell命令都会进行说明，cd啥的就不说了...）

## 开始

### docker运行jenkins

1.  **服务器** 安装docker

- [mac服务器安装教程](https://link.jianshu.com?t=https://docs.docker.com/engine/installation/mac/)
- [linux服务器安装教程](https://link.jianshu.com?t=https://docs.docker.com/engine/installation/linux/)
- [windows服务器安装教程](https://link.jianshu.com?t=https://docs.docker.com/engine/installation/windows/)

1.  **服务器**拉取docker jenkins镜像：`docker pull jenkins:latest` 

- 拉取jenkins latest版本镜像
- docker pull jenkins:2.7.4 拉取jenkins 2.7.4版本镜像

1. **服务器**运行dokcer jenkins容器:`sudo docker run -d --name jenkins_node -p 49002:8080 -v /var/jenkins_node:/var/jenkins_home jenkins:latest`

   - 这句命令的意思是：在后台运行一个基于jenkins:latest镜像的容器, 容器的名字叫做 jenkins_node ,把容器的8080端口映射为49002端口，并且把服务器上的/var/jenkins_node目录挂在到docker容器上的/var/jenkins_home目录

   - ```
     -d
     ```

      后台运行docker容器 

     - 如果不加`-d`则，容器运行会占用此终端，如果终端关闭，则容器也相应关闭，jenkins就无法访问了。加上`-d`,容器会在后台运行。

   - ```
     --name
     ```

      为容器起个别名 

     - 如果不起别名，则系统会默认分配一个随机别名，类似gklasd_sdfwe。起了别名后，后续会通过该别名管理该docker容器，也就是管理jenkins。

   - ```
     -p
     ```

      docker容器端口映射 

     - jenkins服务是运行在docker里的，docker默认不对外暴露端口的

   - ```
     -v
     ```

      文件挂载 

     - 如果不挂载，则jenkins所有log、用户配置文件都会在docker容器内，如果容器销毁，则jenkins得重新配置一遍。挂载出来方便jenkins迁移以及管理

     - 运行成功截图

       

       ![img](https:////upload-images.jianshu.io/upload_images/1300665-a90461fcd58fdc55.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

       jenkins run

2. 查看你服务器上的的/var/jenkins_node文件夹,当出现下图所有文件时，则说明jenkins启动成功，否则再等等...30秒左右即可

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-a83cd140e0bc6232.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/749/format/webp)

   注意：这里会出现/var/jenkins_node文件夹没有操作权限的问题，执行`docker ps`命令，找不到运行的容器，执行`docker ps -a`命令，可以找到该容器；执行` docker logs -f -t jenkins_node`命令查看log，日志显示` touch: cannot touch '/var/jenkins_home/copy_reference_file.log': Permission denied`；只要`chmod +777 /var/jenkins_node`赋权限给文件夹，然后执行`docker start jenkins_id`即可启动。

   ![1550043825945](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1550043825945.png)

   ![1550043864134](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1550043864134.png)

3. 不出意外，浏览器访问[http://host:49002](https://link.jianshu.com?t=http://host:49002) ，会出现如下界面

   - 

     ![img](https:////upload-images.jianshu.io/upload_images/1300665-dbdf6cbc78e7d23c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/996/format/webp)

     

   - 打开initialAdminPassword文件，复制文件内容 initialAdminPassword文件位置为：

     

     ![img](https:////upload-images.jianshu.io/upload_images/1300665-f37fc8f413822db4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/772/format/webp)

     为了方便，用mac演示一下

4. initialAdminPassword文件内容粘贴到**Administrator password**里

5. 如果对于jenkins比较熟悉，下面7,8自己随意

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-85ac0e0e4e8ce926.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/992/format/webp)

   

6. 等...

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-4fb6c95252d4666b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/988/format/webp)

   关于插件下载失败的部分，待补充。

7. 设置admin user

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-c9a2ef996529f937.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/986/format/webp)

   

### node项目配置

> - 示例项目：[https://git.oschina.net/ryzecode/HelloWorld_Node.git](https://link.jianshu.com?t=https://git.oschina.net/ryzecode/HelloWorld_Node.git) 
> - 比正常的node项目在根目录多一个Dockerfile文件

### jenkins配置

#### 插件安装

1. 首页，点击系统管理 --> 管理插件

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-764837926d78bbd3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

   

2. 选择“可选插件”

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-db1a2e6dad372107.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

   

3. 右上角过滤SSH，--> 选择Publish Over SSH -->点击 直接安装

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-17dd1a02b9aec5ad.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

   

4. 等待安装完成

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-cffc3883e5235b04.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/655/format/webp)

   

#### 系统设置

1. 首页，点击系统管理 --> 系统设置

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-351629a1d993cae4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

   

2. 拉到最下面找到 "Publish over SSH" , 设置服务器的SSH信息

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-e30eb60de03c66d4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

   

#### 项目配置

1. 首页，点击左上角”新建”

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-52918c464a3e249c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

   

2. General： 只填 项目名称即可

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-81969f89f28c36f0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/948/format/webp)

   

3. 源码管理： 填写Git信息

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-f6970d305306b7bd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/936/format/webp)

   

- 如果Git项目为私有项目，则需要点击Add添加你的Git账号，完成之后在这里选择你的Git账号

  

  ![img](https:////upload-images.jianshu.io/upload_images/1300665-36c923cd70f2136e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

  

1. 构建环境：参照下图

- 

  ![img](https:////upload-images.jianshu.io/upload_images/1300665-c7713a4258634078.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/979/format/webp)

  

- 图中代码如下： 

  ```
  sudo docker stop node || true \
       && sudo docker rm node || true \
       && cd /var/jenkins_node/workspace/node \
       && sudo docker build --rm --no-cache=true  -t node  - < Dockerfile \
       && sudo docker run -d  --name node -p 3000:3000 -v /var/jenkins_node/workspace/node:/home/project node
  ```

1. 保存，返回到首页
2. 选择刚刚创建的项目，点击"立即构建"

- 此时**服务器**本地硬盘上，/var/jenkins_node/workspace/下应该有node（你创建的jenkins项目名）文件夹

- /var/jenkins_node/ 目录内容应该如下图

  

  ![img](https:////upload-images.jianshu.io/upload_images/1300665-a8a71d87bc1ac5cc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/612/format/webp)

  为了方便查看，继续用mac演示目录

- 构建成功，浏览器输入 

  http://host:3000

   应该能看到下图内容

  

  ![img](https:////upload-images.jianshu.io/upload_images/1300665-ddb811ba2303213f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/679/format/webp)

  

### 自动部署

> 如果想要git pull，jenkins就自动部署，那就继续往下看...

1. jenkins --> 首页 --> 用户

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-429361881a12bb17.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

   

2. 设置 --> 点击 "show API Token"

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-ec53b892a4733723.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

   

3. 复制API Token内容

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-b72baf25ecbd59e4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

   

4. 返回首页 --> node --> 配置 --> 构建触发器 --> 选择 "触发远程构建" -->粘贴"API Token"内容到"身份验证令牌"

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-6bd75fdc21020d9c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/955/format/webp)

   

5. 登陆git平台，找到你的项目，选择管理，选择webhook

   

   ![img](https:////upload-images.jianshu.io/upload_images/1300665-c3e217506d00a70c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

   

- URL 中 node 请替换为你在jenkins上创建的项目名称
- token为复制的 API Token内容

1. 此时你git push，会发现jenkins并没有自动构建，是因为jenkins的安全策略导致的，还需要如下设置 

   - jenkins首页，选择 系统管理-->Configure Global Security(系统设置下面那个) 进行如下设置

     

     ![img](https:////upload-images.jianshu.io/upload_images/1300665-392e2bcc992743f1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

     

至此，已完成 git push 后，jenkins 自动构建自动部署。

对于**Docker**感兴趣，可以看下这篇文章：

### Docker常用命令

**docker hub login**
 $ docker login

**run**
 $ docker run -t -i -p 9001:9001 -p 9099:9099 ryzebo/scu:v1 /bin/bash

**commit**
 $ sudo docker commit -m="Added json gem" -a="Kate Smith" 0b2616b0e5a8 ouruser/sinatra:v2

**push **
 $ sudo docker push ryzebo/scu:v1.1

**删除**
 删除images             docker  rmi  <imageId>
 删除container         docker rm <container_id>
 删除退出了的容器: docker rm `docker ps -a | grep Exited | awk '{print $1 }’

**查看**
 查看所有images     docker images
 查看所有容器          docker ps -a
 查看运行的容器      docker ps -l
 过滤                        docker ps -a --filter "ancestor=data-express-verify"
 stop容器                 docker stop `docker ps -a --filter "ancestor=data-express-verify"`
 查找none images  docker images --filter "dangling=true"

**重启容器**
 sudo docker stop <container_id>

docker ps -l
 docker start 7cf737095e10

**进入容器**
 docker attach infallible_lamport

**--link**
 docker run -d -P --name web --link db:db training/webapp python app.py

**copy 文件 至container**
 ADD ./releases/src /sat-example

**dockerfile 设置默认工作目录**
 WORKDIR /sat-example

**dockerfile**
 docker build --rm --no-cache=true  -t data-express-verify . && docker run -p 3000:3000  data-express-verify

**Jenkins  **-v

------

docker run -d --name jenkins_roboware -p 49002:8080 -v /var/jenkins_roboware_home:/var/jenkins_home jenkins

docker run -d --name jenkins_roboware -p 8080:8080 -v /home/ubuntu/jenkins_home:/var/jenkins_home jenkins

------

------

------

**系统相关**
 **获取root权限  sudo passwd root**

------

------

查看系统内核  uname -a

列出所有目录 （tree）：ls -R | grep ":$" | sed -e 's/:$//' -e 's/[-][/]*//--/g' -e 's/^/   /' -e 's/-/|/' 删除文件夹  ： rm -rf dirname 清空回收站：rm -f -r ~/.local/share/Trash/files/*
 持续输出 netstat信息：netstat -c

