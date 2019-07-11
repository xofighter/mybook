**Docker构建镜像的方法主要有两种：**

​    （1）使用docker commit命令;



​    （2）使用docker build命令和Dockerfile文件（更为强大、灵活和常用）;



**一：准备centos镜像**

```html
docker pull centos
```

![img](https://img-blog.csdn.net/20180627100311969?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3OTM2NTQy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



**二：下载jdk、tomcat安装包，上传/usr/local/soft目录下**

jdk1.8下载：<http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html>

tomcat8下载：<https://tomcat.apache.org/download-80.cgi>

![img](https://img-blog.csdn.net/20180627095043601?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3OTM2NTQy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



**三：解压，创建Dockerfile文件**

```html

```

1. tar -zxvf apache-tomcat-8.5.31.tar.gz #解压tomcat
2. 
3. tar -zxvf jdk-8u171-linux-x64.tar.gz #解压jdk
4. 
5. rm -rf apache-tomcat-8.5.31.tar.gz #删除安装包
6. 
7. rm -rf jdk-8u171-linux-x64.tar.gz #删除安装包
8. 
9. touch Dockerfile #创建文件

操作完成之后，soft目录内容如下：

![img](https://img-blog.csdn.net/2018062709590432?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3OTM2NTQy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



**四：编写Dockerfile文件**

```html

```

1. \#指定操作的镜像
2. FROM centos
3. 
4. \# 维护者信息
5. MAINTAINER biubiu
6. 
7. \#执行命令：创建目录
8. RUN mkdir -p /usr/local/soft
9. \#将jdk1.8.0_171添加到镜像centos的/usr/local/soft/目录下，并命名为jdk
10. ADD jdk1.8.0_171 /usr/local/soft/jdk
11. \#将apache-tomcat-8.5.31添加到镜像centos的/usr/local/soft/目录下，并命名为tomcat
12. ADD apache-tomcat-8.5.31 /usr/local/soft/tomcat
13. 
14. \#添加环境变量
15. ENV JAVA_HOME /usr/local/soft/jdk
16. ENV CATALINA_HOME /usr/local/soft/tomcat
17. ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/bin
18. 
19. \#暴露8080端口
20. EXPOSE 8080
21. 
22. \#启动时运行tomcat
23. CMD ["/usr/local/soft/tomcat/bin/catalina.sh","run"]

FROM : 指定基础镜像，并且必须是第一条指令

MAINTAINER ： 指定作者

RUN : 运行指定的命令

ADD : 复制命令，把文件复制到镜像中。

ENV : 设置环境变量

EXPOSE : 功能为暴漏容器运行时的监听端口给外部

CMD : 指定容器启动时运行的命令



**五：构建Docker镜像**

```html
docker build -t repostory/centos_tomcat .
```

-t 设置tag名称, 命名规则registry/image:tag（若不添加版本号,默认latest）
. 表示使用当前目录下的Dockerfile文件（注意语句后面有一个点）



**六：启动镜像，访问**

```html
docker run -d -p 8080:8080 --name Icentos repostory/centos_tomcat
```

-d 后台运行     

-p 端口映射  宿主机port : 容器port

--name 指定容器运行名称

![img](https://img-blog.csdn.net/20180627103509295?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3OTM2NTQy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)