### 前言

Docker-compose是一个定义和运行多个Docker应用的工具，你可以使用YMAL文件来配置你的服务，然后使用docker-compose命令，创建和启动所有你配置的的服务。compose可以在任何工作环境中使用，生产环境，开发环境，持续集成等等。



![img](https://upload-images.jianshu.io/upload_images/426671-a5421ee1dfc44a19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/260/format/webp)

Docker

使用compose一般包括三个步骤：

1. 使用`Dockerfile`定义App的环境，以便于可再次重复利用
2. 在`docker-compose.yml`文件中定义服务，以便于多个Docker应用可以协作起来
3. 最后，运行`docker-compose up`，然后compose回启动和运行整个App。

### 操作

确保你已经安装了Docker和Docker compose。

##### 设置

1. 创建目录

```
mkdir composetest
cd composetest
```

1. 创建app.py文件，python文件有严格的行缩进关系。如果缩进不一样会报错。

```
from flask import Flask
from redis import Redis

app = Flask(__name__)
redis = Redis(host='redis', port=6379)

@app.route('/')
def hello():
    count = redis.incr('hits')
    return 'Hello From Aihe! I have been seen {} times.\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```

1. 创建另外一个依赖文件`requirements.txt`，python的依赖关系。

```
flask
redis
```

##### 创建Dockerfile文件

内容如下

```
FROM python:3.4-alpine
ADD . /code
WORKDIR /code
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

##### 在compose文件定义服务

文件内容如下

```
version: '3'
services:
  web:
    build: .
    ports:
     - "5000:5000"
    volumes:
     - .:/code

  redis:
    image: "redis:alpine"
```

这个文件包含两个服务，`web`与`redis`
web服务

- `Build` 使用Dockerfile来构建镜像
- `Ports` 主机转发5000端口到容器对应的5000端口
- `Volumes` 挂载当前目录到容器内的/code目录下，我们可以直接修改本地文件，然后容器内会立即生效

##### 构建和运行app

1. 在当前目录下运行docker-compose up
   当前目录结构为

   

   ![img](https://upload-images.jianshu.io/upload_images/426671-c2901c5163045011.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

   当前目录内容

```
docker-compose up
```

1. 在浏览器中输入`http://localhost:5000/`







![img](https://upload-images.jianshu.io/upload_images/426671-f1b7ac8e63d1e8d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/966/format/webp)

测试结果

我们可以以修改app.py文件中return的内容，可以看到浏览器立即生效。

### 额外

1. 在配置的过程中，可能compose文件配置有错误，到底第一次无法启动服务。这时候重新构建服务。

```
docker-compose build --no-cache
```

1. 后台运行与停止compose服务

```
docker-compose up -d
docker-compose stop
```

### 最后

Docker确实给我们提供了极大的便利，使用的过程发现有以下好处

- 本地环境无需更改，直接使用Docker容器做测试
- 搭建集群直接使用容器，一台机器也可以完成

这里简单的介绍了docker-compose的用法，希望能对大家有帮助。