# **简介**

[*Swagger*– The World's Most Popular Framework for APIs.](https://link.jianshu.com?t=https://www.baidu.com/link?url=eIZGj3wXarGfrF-OVOPCkFmBI4c_G1Fh0eyLTpsTllK&wd=&eqid=f6e082c700024f9600000005598ad9f5)

Swagger 是一款RESTFUL接口的文档在线自动生成+功能测试功能软件。本文主要介绍在项目开发前期Swagger本地环境的搭建，包括Swagger UI 和Swagger Editor。

# 环境

Node.js：         v7.6.0

Npm：                4.1.2

依赖： Express，http-server

# Swagger UI

#### 1、下载Swagger UI

*git@github.com:swagger-api/swagger-ui.git*

#### **2、创建项目文件夹**

*mkdir myapp*

#### 3、初始化myapp

进入myapp目录初始化

*npm init*

此时目录下会生产package.json文件

#### 4、安装Express

*npm install express --save*

#### 5、创建index.js

写入如下代码：

*var express = require('express');*

*var app = express();*

*app.get('/', function (req, res) {*

*res.send('Hello World!');*

*});*

*app.listen(3000, function () {*

*console.log('Example app listening on port 3000!');*

*});*

#### 6、在myapp目录中创建public文件夹

修改第5步中的index.js文件，在文件第三行添加如下代码：

*app.use('/static', express.static('public'));*

#### 7、把下载好的Swagger UI 文件中dist 目录下的文件全部复制到 public 文件夹下  

#### 8、启动node

*node index.js*

#### 9、打开浏览器，输入[http://localhost:3000/static/index.html](https://link.jianshu.com?t=http://localhost:3000/static/index.html) 

# Swagger Editor

#### 1、下载Swagger Editor

*git@github.com:swagger-api/swagger-editor.git*

#### 2、安装http-server

*npm install http-server -g*

#### 3、启动

*http-server swagger-editor*

默认为8080端口，如果要修改端口号，使用如下命令：*http-server –p 2008 swagger-editor*

#### 4、浏览器访问

*http://127.0.0.1:8080*

到此，基本上本地环境搭建成功了，使用Editor生成的文档，可以使用UI查看，主需要修改index.html文件中的url即可，如有疑问欢迎留言交流，谢谢！

作者：阿杰Alex

链接：https://www.jianshu.com/p/98c6b55fb13a

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。