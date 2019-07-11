## Java项目目录解释
1.工程“创作”出的网页根目录是 WebRoot目录，其下有META-INF文件夹（储存的是保存程序运行时加载的主类路径的Manifest文件），WEB-INF文件夹下储存（xml文件（储存servlet信息）、classes文件夹(储存编译好的java类文件)、lib文件夹（储存外部导入的包比如jdbc的mysql驱动jar包））、jsp文件（html混合java代码网页文件）。

2.http://localhost:8080/项目名/=WebRoot下默认访问的index.jsp

http://localhost:8080/WebTest/（servlet名或者逻辑名）=xml文件中对应的servlet名/逻辑名

3.网页打开流程：输入url  ->访问相应的项目根目录（webroot）->找到xml文件->选择相应的servlet->找到对应的servlet类文件->完成工作后响应请求。

Java项目目录一览
```
附一个javaweb项目的目录结构

├── pom.xml //maven的配置文件
└── src
    ├── main
    │   ├── java //java代码的目录
    │   │   └── mygroup
    │   │       ├── controller
    │   │       │   ├── HomeController.java
    │   │       │   └── PersonController.java
    │   │       ├── dao
    │   │       │   └── PersonDao.java
    │   │       └── model
    │   │           └── Person.java
    │   ├── resources //静态资源目录
    │   │   ├── db.properties
    │   │   ├── log4j.xml
    │   │   └── META-INF
    │   │       └── persistence.xml
    │   └── webapp //web应用部署根目录
    │       ├── index.html //因为是静态html文件，不用放到WEB-INF目录下
    │       ├── META-INF
    │       │   ├── context.xml
    │       │   └── MANIFEST.MF
    │       ├── resources //css，js等静态资源是不能放到WEB-INF目录下的，因为WEB-INF下的资源，客户端无法直接访问
    │       │   └── css
    │       │       └── screen.css
    │       └── WEB-INF //jsp都会放到这里，以保证用户无法直接访问jsp，而是通过controller这个目录下的所有内容客户端都无法直接访问，所以不要放静态文件
    │           ├── spring
    │           │   ├── app
    │           │   │   ├── controllers.xml
    │           │   │   └── servlet-context.xml
    │           │   ├── db.xml
    │           │   └── root-context.xml
    │           ├── views
    │           │   ├── edit.jsp
    │           │   ├── home.jsp
    │           │   └── list.jsp
    │           └── web.xml
    └── test
        ├── java
        │   └── mygroup
        │       ├── controller
        │       │   ├── DataInitializer.java
        │       │   ├── HomeControllerTest.java
        │       │   └── PersonControllerTest.java
        │       └── dao
        │           └── PersonDaoTest.java
        └── resources
            ├── db.properties
            ├── log4j.xml
            ├── test-context.xml
            └── test-db.xml
```

------
## java项目和java-web项目中文件和文件夹的含义

 1. java项目

- .project:是工程构建配置文件

- .classpath:保存的是项目所用的外部引用包的路径

- .settings：记录项目配置变化的记录文件夹

- src:sourcefolder项目源代码.java文件的存放位置

- bin：项目开发时的classes文件存放目录：项目编译后.class文件的存放位置

```
`注： 右键点击项目名称 - buildpath - source标签：编辑项目的sourcefolder和outputfolder`
```

2. java - web项目

- .project:是工程构建配置文件
- .classpath:保存的是项目所用的外部引用包的路径
- .settings：记录项目配置变化的记录文件夹
- src:sourcefolder项目源代码.java文件的存放位置
- build/classes：项目开发时的classes文件存放目录：既项目编译后.class文件的存放位置
- webcontent/web-info/classes:项目发布到tomcat时， .class文件的存放目录

> 部分转载于 :
> https://blog.csdn.net/li99yangg/article/details/79396318
> https://www.cnblogs.com/nelson-hu/p/7163736.html