前文[使用Docker搭建Jenkins+Docker持续集成环境](https://link.jianshu.com?t=http%3A%2F%2Fwww.cnblogs.com%2Fxiaoqi%2Fp%2Fdocker-jenkins-cicd.html)我们已经搭建了基于docker+jenkins的持续集成环境，并构建了基于maven的项目。这一节，我们继续扩展功能，增加对Nodejs的支持,实现nodejs项目构建、并打包成docker镜像和自动部署。

## 1. 配置Nodejs环境

### 1.1 安装nodejs插件

打开系统管理——管理插件——可选插件，搜索NodeJS，选择NodeJS Plugin安装

### 1.2 配置nodejs 版本

系统管理 —— 全局工具配置 —— NodeJS，选择安装nodejs，选择当前最新版本9.4，命名NodeJS 9.4。

## 2. 配置项目

### 2.1 新建项目

新建jenkins项目，选择自由项目，按前文说明配置好SVN、触发器。

在构建环境里，选择Provide Node & npm bin/ folder to PATH，选择我们配置的9.4版本nodejs

### 2.2 配置构建命令

一般是通过npm命令构建，我们选择增加构建步骤 —— Excute shell，输入构建命令：

```
alias cnpm="npm --registry=https://registry.npm.taobao.org \
--cache=$HOME/.npm/.cache/cnpm \
--disturl=https://npm.taobao.org/dist \
--userconfig=$HOME/.cnpmrc"
cnpm install 
cnpm run build
```

注意，这里为了构建更快，选择通过alias增加cnpm指令，指定使用淘宝的仓库。

### 2.3 构建docker镜像

由于我们构建出来的已经是可访问的资源了，放在dis目录，所以我们可以基于nginx作为基础镜像。
 编写DockerFile：

```
FROM nginx
ADD ./dist /usr/share/nginx/html
EXPOSE 80
```

然后，增加构建步骤，ADD build/publish docker image
 设置image：192.168.86.8:5000/allinone-web-cicd
 勾上push image，会自动push到192.168.86.8:5000仓库



enter description here

### 2.4 自动部署镜像

和上文一样，这里继续使用ssh实现docker镜像部署。

增加构建步骤，Execute shell script on remote host using ssh：

选择docker swarm的manager机器，输入命令：

```
docker service rm  allinone-web-cicd
docker service create --name allinone-web-cicd --replicas 1 --publish 10081:80 192.168.86.8:5000/allinone-web-cicd
```

这次，我们使用docker service来实现部署，先service rm掉老服务，然后create新服务。

## 3.测试构建

点击立即构建：



enter description here

稍等片刻，就构建成功了；

```
[SSH] executing...
allinone-web-cicd
mj9dwq00ath03i05b8bfe5plx
overall progress: 0 out of 1 tasks
1/1:  
overall progress: 0 out of 1 tasks
overall progress: 0 out of 1 tasks
overall progress: 0 out of 1 tasks
overall progress: 0 out of 1 tasks
overall progress: 0 out of 1 tasks
overall progress: 0 out of 1 tasks
overall progress: 1 out of 1 tasks
verify: Waiting 5 seconds to verify that tasks are stable...
verify: Waiting 5 seconds to verify that tasks are stable...
verify: Waiting 5 seconds to verify that tasks are stable...
verify: Waiting 5 seconds to verify that tasks are stable...
verify: Waiting 5 seconds to verify that tasks are stable...
verify: Waiting 4 seconds to verify that tasks are stable...
verify: Waiting 4 seconds to verify that tasks are stable...
verify: Waiting 4 seconds to verify that tasks are stable...
verify: Waiting 4 seconds to verify that tasks are stable...
verify: Waiting 4 seconds to verify that tasks are stable...
verify: Waiting 3 seconds to verify that tasks are stable...
verify: Waiting 3 seconds to verify that tasks are stable...
verify: Waiting 3 seconds to verify that tasks are stable...
verify: Waiting 3 seconds to verify that tasks are stable...
verify: Waiting 3 seconds to verify that tasks are stable...
verify: Waiting 2 seconds to verify that tasks are stable...
verify: Waiting 2 seconds to verify that tasks are stable...
verify: Waiting 2 seconds to verify that tasks are stable...
verify: Waiting 2 seconds to verify that tasks are stable...
verify: Waiting 2 seconds to verify that tasks are stable...
verify: Waiting 1 seconds to verify that tasks are stable...
verify: Waiting 1 seconds to verify that tasks are stable...
verify: Waiting 1 seconds to verify that tasks are stable...
verify: Waiting 1 seconds to verify that tasks are stable...
verify: Waiting 1 seconds to verify that tasks are stable...
verify: Service converged

[SSH] completed
[SSH] exit-status: 0

Finished: SUCCESS
```

这个时候，访问swarm集群的任一[http://ip:10081](https://link.jianshu.com?t=http%3A%2F%2Fip%3A10081)，就可以看到效果了。

作者：JadePeng

链接：https://www.jianshu.com/p/4b170d2dfd8b

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。