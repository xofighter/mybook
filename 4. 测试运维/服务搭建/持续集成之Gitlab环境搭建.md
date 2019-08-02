## 持续集成之Gitlab环境搭建

[TOC]

--------------

​       在持续集成和持续部署的技术栈中，Gitlab也是非常重要的部分，Gitlab主要应用于代码版本管理，不管是什么样的公司，都会应用到代码版本管理的部分。Gitlab它是基于Ruby On Rails开发的Git项目仓库，它的官方地址是:https://gitlab.com/，想深入了解的同学可以到Gitlab的官方去看它的doc的文档说明。在本文中主要今天主要介绍Gitlab环境的搭建，操作系统是Centos7.5（特别强调说明，如果是低配置的服务器，搭建Gitlab服务器后，启动Gitlab后，服务器的占用会被占用很多，所以需要特别的注意）。

​     在安装Gitlab之前，首先需要在Linux中安装它的依赖部分，主要涉及到的如下：

```shell
yum install curl policycoreutils openssh-server openssh-clients
systemctl enable sshd
systemctl start sshd
yum install postfix
systemctl enable postfixs
ystemctl start postfix
yum install -y policycoreutils-python
```



​     下来就是到官方下载Gitlab的安装包，该安装包六百多兆，在Linux里面直接使用wget下载，下载的命令为：

```
wget --content-disposition https://packages.gitlab.com/gitlab/gitlab-ce/packages/el/7/gitlab-ce-12.0.2-ce.0.el.x86_64.rpm/download.rpm
```

下载文件成功后，会显示在本地，见如下图所示：

![img](https://mmbiz.qpic.cn/mmbiz_png/Xia9Sia2oqUtkfyopEAf15HdlJPHxrhIdic3WuFYpTj5hVgO7ticHge45OQpbvXMcn8EiaXfl6YHzcE31nS4OibfnAAA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

执行如下命令直接安装，执行的命令为：

```
rpm -i gitlab-ce-12.0.2-ce.0.el7.x86_64.rpm
```

安装成功后，开始启动Gitlab，首次启动Gitlab时间比较长，启动的命令为：

**gitlab-ctl reconfigure**

见启动的截图信息：

![img](https://mmbiz.qpic.cn/mmbiz_png/Xia9Sia2oqUtkfyopEAf15HdlJPHxrhIdicF0jZmsY6WEZqYoK3e3Yj20FXfpQPucpC2ISxaOIDmwZNqvwWbsibF1A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



本人已经安装过，所以只需要执行gitlab-ctl start来启动gitlab的服务，见执行后的命令截图：

![img](https://mmbiz.qpic.cn/mmbiz_png/Xia9Sia2oqUtkfyopEAf15HdlJPHxrhIdica8Nk1gNEDMFprCuXRialoaZnND6oDxZdrmk1T3fLIqze557f6pibNjfg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

启动成功后，系统的资源会被占用很多的，启动内存会被占用1G，见目前服务器的资源信息

![img](https://mmbiz.qpic.cn/mmbiz_png/Xia9Sia2oqUtkfyopEAf15HdlJPHxrhIdictXVvWU0F92UDzwMqsN1JELAxY1AfJIHmQIdLElXicNeP01X22Oe9OnQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

查询gitlab：

![img](https://mmbiz.qpic.cn/mmbiz_png/Xia9Sia2oqUtkfyopEAf15HdlJPHxrhIdicZm7lJHPjMnhNoILVVD1KKIhrIpeHvQQn6RQS3QkHXjRRqbF3XkJdzQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在阿里云服务器需要开放端口,gitlab访问默认的端口是80，所以需要开放出来，然后在浏览器访问http://x.x.x.x就会显示Gitlab的登录页面，见如下截图：

![img](https://mmbiz.qpic.cn/mmbiz_png/Xia9Sia2oqUtkfyopEAf15HdlJPHxrhIdicFjIDBPXlBUIZjpvEAkk68llhnSnPKibbffaEDhgpmECDV4gfcK80YTw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

首次登录Gitlab需要重置密码，用户是root，设置密码后，就可以登录成功，见登录成功后的截图信息：

![img](https://mmbiz.qpic.cn/mmbiz_png/Xia9Sia2oqUtkfyopEAf15HdlJPHxrhIdicyHeFenuvaP4ics7C4q4958DlANb5wuHAiaIagZSXOThECg7GeEMV5RxA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

​    gitlab会有很多的组件，如nginx等，所以就涉及到如何查看日志信息，如果是查询所有的log信息，执行的命令是：gitlab-ctl tail，如下图所示：

![img](https://mmbiz.qpic.cn/mmbiz_png/Xia9Sia2oqUtkfyopEAf15HdlJPHxrhIdicoEhtOdWzE3PSa3wOJueZYBfBiaiavOWNibScV2ZU4fgia2Mty86NeC1PaQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

假设查询nginx的log，执行的命令为:gitlab-ctl tail ${nginx}，如下图所示：

![img](https://mmbiz.qpic.cn/mmbiz_png/Xia9Sia2oqUtkfyopEAf15HdlJPHxrhIdicz9JRI7MNNibwW1ZFBuBsNjtHXt2pEXyia0dAU31tRKd1349Yj8oaBgpQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

​       接下来回逐步的更新Gitlab的基本配置信息，与Jenkins的集成获取代码在自动化测试中的应用。后续会陆续的更新这些知识体系。

