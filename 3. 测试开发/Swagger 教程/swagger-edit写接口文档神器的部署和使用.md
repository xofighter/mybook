# swagger-edit写接口文档神器的部署和使用

## 介绍

swagger 是一套开源的API设计工具，包括Swagger UI，Swagger Editor等。

其中Swagger Editor是个WEB小程序，它可以让你用YAML来定义你的接口规范，并实时验证和现实成接口文档。

我们主要使用Swagger Editor 编写接口文档，在线 [Swagger Editor](https://link.jianshu.com/?t=https://editor.swagger.io) 也是可以使用的，但是访问速度在国内有点慢，所以为了效率，自己在服务器搭建一套服务

## 下载

直接git clone 最新代码：

```
git clone https://github.com/swagger-api/swagger-editor.git
```

## 安装

> 首先确保有node.js 的环境

```
cd swagger-edito
npm install
```

> 安装 PM2 ,如果不会PM2，请查看《使用PM2运行node.js服务》

## 运行

使用PM2运行

```
pm2 start npm -- start
```

## 使用

访问：

http://192.168.2.52:3001/

## 参考：

- [http://blog.csdn.net/wangmeng951011/article/details/67640375](https://link.jianshu.com/?t=http://blog.csdn.net/wangmeng951011/article/details/67640375)