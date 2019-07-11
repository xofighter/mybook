> 转自：[Ubuntu 18.0.4安装docker](https://www.cnblogs.com/daner1257/p/10197855.html) 	

----

# Ubuntu 18.0.4安装docker

第一步：如果之前安装过docker，执行下面命令删除

```
apt-get remove docker docker-engine docker.io
```

删除后执行sudo apt-get update更新软件

第二步：安装必要的软件包以允许apt通过HTTPS使用存储库，具体如下：

```
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
```

第三步：添加GPG密钥，可以添加官方的和阿里的，我添加 的阿里的，国内的快啊

```
// 官方
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
// 阿里
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

添加完毕后可以执行以下命令验证：

```
sudo apt-key fingerprint 0EBFCD88
```

\```正常情况下会输出如下内容：`

```
pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ 未知 ] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]
```

说明 Ok，继续

第四步：设定稳定仓储库，这一步我被 坑了好久，具体参考

[docker配置仓储库时出错：无法安全地用该源进行更新，所以默认禁用该源](https://www.cnblogs.com/daner1257/p/10197801.html)

也可以不设置，不设置默认使用官方的，具体是：deb [arch=amd64] <https://download.docker.com/linux/ubuntu> xenial stable

同样可以用阿里 的镜像：设置命令如下： 

```
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

其中的lsb_release -cs相当于一个函数，直接获取Ubuntu下的最新版本

设置完毕再次执行sudo apt-get update命令更新 软件包。

第五步：安装docker，如下：

```
sudo apt-get -y install docker-ce
```

也可以指定想安装 的docker版本，方法 如下：

执行如下命令命令查看有哪些版本，

```
apt-cache madison docker-ce
```

输出如下：

```
 docker-ce | 5:18.09.0~3-0~ubuntu-bionic | http://mirrors.aliyun.com/docker-ce/linux/ubuntu bionic/stable amd64 Packages
 docker-ce | 18.06.1~ce~3-0~ubuntu | http://mirrors.aliyun.com/docker-ce/linux/ubuntu bionic/stable amd64 Packages
 docker-ce | 18.06.0~ce~3-0~ubuntu | http://mirrors.aliyun.com/docker-ce/linux/ubuntu bionic/stable amd64 Packages
 docker-ce | 18.03.1~ce~3-0~ubuntu | http://mirrors.aliyun.com/docker-ce/linux/ubuntu bionic/stable amd64 Packages
```

选择要安装的版本，执行`sudo apt-get install -y docker-ce=<VERSION>`命令即可。

安装完成 后执行docker -v命令，如果正常输出说明安装成功，下面继续填坑吧。

 

最后更新个CentOs上安装的博客，挺好的，关键是写了如何在安装前配置国内镜像（[传送门](https://www.cnblogs.com/OnlyDreams/p/8990350.html)），不然安装要坑死人了网速。