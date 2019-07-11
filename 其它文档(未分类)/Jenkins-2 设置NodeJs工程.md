![1559616089534](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1559616089534.png)

![1559616106905](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1559616106905.png)

![1559616127752](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1559616127752.png)

![1559616142441](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1559616142441.png)

![1559616163721](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1559616163721.png)

![1559616181916](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1559616181916.png)



Dockerfile

```dockerfile
FROM node:10.1.0

MAINTAINER xxxxx

RUN mkdir -p /ep-vue-public

# 讲本地目录拷贝到镜像目录
ADD /ep-vue-public /ep-vue-public

# 指定工作目录
WORKDIR /ep-vue-public

# 安装依赖及构建node应用
# RUN npm install --registry=https://registry.npm.taobao.org
RUN yarn
RUN npm run buildUat

# 配置系统变量，指定端口
ENV PORT 3002

# 将端口3002开放
EXPOSE 3002

# 容器启动命令
CMD npm run startUat

```

Dockerfile2

```dockerfile
FROM node:10.15.3

LABEL maintainer="xxxx@e-ports.com"

RUN mkdir -p /target

# 讲本地目录拷贝到镜像目录
ADD /ad-portal /target

# 指定工作目录
WORKDIR /target

ENV EP_ENV development
ENV EP_PORT 9010

# 安装依赖及构建node应用
RUN npm install adui-domain --registry=http://npm.e-ports.com
RUN npm install adui-reducer --registry=http://npm.e-ports.com
RUN npm install epimmutable --registry=http://npm.e-ports.com
RUN npm install --registry=https://registry.npm.taobao.org
RUN npm run build

# 配置系统变量，指定端口
ENV PORT 9010

# 将端口9010开放
EXPOSE 9010

# 容器启动命令
CMD npm run server

```



build.sh

```shell
#!/bin/bash

echo "构建开始时间："`date -R`

cd ep-vue-public/ && git pull
# tar -zxvf ep-vue-public.tar.gz
docker stop ep-vue-public_test && docker rm ep-vue-public_test && docker rmi ep-vue-public
cd ../ && docker build -t ep-vue-public .
docker run -d -p 3002:3002 --name ep-vue-public_test -m 300M --memory-swap -1 --restart=always -v /etc/localtime:/etc/localtime ep-vue-public
# rm -rf ep-vue-public.tar.gz && rm -rf ep-vue-public/
echo "构建完成时间："`date -R`

```

build2.sh

```shell
#!/bin/bash

adddate() {
    while IFS= read -r line; do
        echo "$(date) $line"
    done
}

./build2.sh | adddate >>output
```
