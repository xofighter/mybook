教程基于ubuntu下最新的compose版本3 
参考： https://docs.docker.com/compose/overview/

1.Compose介绍
Docker Compose是一个用来定义和运行复杂应用的Docker工具。一个使用Docker容器的应用，通常由多个容器组成。使用Docker Compose不再需要使用shell脚本来启动容器。 
Compose 通过一个配置文件来管理多个Docker容器，在配置文件中，所有的容器通过services来定义，然后使用docker-compose脚本来启动，停止和重启应用，和应用中的服务以及所有依赖服务的容器，非常适合组合使用多个容器进行开发的场景。

2.Compose和Docker兼容性
| compose文件格式版本 | docker版本 |
| -- | -- |
| 3.4 | 17.09.0+ |
| 3.3 | 17.06.0+ |
| 3.2 | 17.04.0+ |
| 3.1 | 1.13.1+ |
| 3.0 | 1.13.0+ |
| 2.3 | 17.06.0+ |
| 2.2 | 1.13.0+ |
| 2.1 | 1.12.0+ |
| 2.0 | 1.10.0+ |
| 1.0 | 1.9.1.+ |

Docker版本变化说明：
Docker从1.13.x版本开始，版本分为企业版EE和社区版CE，版本号也改为按照时间线来发布，比如17.03就是2017年3月。

Docker的linux发行版的软件仓库从以前的https://apt.dockerproject.org和https://yum.dockerproject.org变更为目前的https://download.docker.com, 软件包名字改为docker-ce和docker-ee。


3.安装docker
Docker的社区版（Docker Community Edition）叫做docker-ce。老版本的Docker包叫做docker或者docker-engine，如果安装了老版本的docker得先卸载然后再安装新版本的docker。docker的发展非常迅速，apt源的更新往往比较滞后。所以docker官网推荐的安装方式都是下载docker安装脚本安装。 
卸载老旧的版本（若未安装过可省略此步）：

$ sudo apt-get remove docker docker-engine docker.io
1
安装最新的docker：

$ curl -fsSL get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
1
2
shell会提示你输入sudo的密码，然后开始执行最新的docker过程 
或者

$ curl -sSL https://get.docker.com/ | sh 
1
确认Docker成功最新的docker：

$ sudo docker run hello-world
1
4.安装docker-compose
两种最新的docker安装方式

1.从github上下载docker-compose二进制文件安装
下载最新版的docker-compose文件 
$ sudo curl -L https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
添加可执行权限 
$ sudo chmod +x /usr/local/bin/docker-compose
测试安装结果 
$ docker-compose --version 
docker-compose version 1.16.1, build 1719ceb
2.pip安装
$ sudo pip install docker-compose

5.docker-compose文件结构和示例
docker-compose文件结构
docker-compose.yml:


version: "3"
services:

  redis:
    image: redis:alpine
    ports:
      - "6379"
    networks:
      - frontend
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure

  db:
    image: postgres:9.4
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend
    deploy:
      placement:
        constraints: [node.role == manager]

  vote:
    image: dockersamples/examplevotingapp_vote:before
    ports:
      - 5000:80
    networks:
      - frontend
    depends_on:
      - redis
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
      restart_policy:
        condition: on-failure

  result:
    image: dockersamples/examplevotingapp_result:before
    ports:
      - 5001:80
    networks:
      - backend
    depends_on:
      - db
    deploy:
      replicas: 1
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure

  worker:
    image: dockersamples/examplevotingapp_worker
    networks:
      - frontend
      - backend
    deploy:
      mode: replicated
      replicas: 1
      labels: [APP=VOTING]
      restart_policy:
        condition: on-failure
        delay: 10s
        max_attempts: 3
        window: 120s
      placement:
        constraints: [node.role == manager]

  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "8080:8080"
    stop_grace_period: 1m30s
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]

networks:
  frontend:
  backend:

volumes:
  db-data:

docker-compose使用示例
通过docker-compose构建一个在docker中运行的基于python flask框架的web应用。

注意：确保你已经安装了Docker Engine和Docker Compose。 您不需要安装Python或Redis，因为这两个都是由Docker镜像提供的。

Step 1: 定义python应用
1 .创建工程目录
$ mkdir compose_test
$ cd compose_test
$ mkdir src      # 源码文件夹
$ mkdir docker  # docker配置文件夹
1
2
3
4
目录结构如下： 


2 .在compose_test/src/目录下创建python flask应用 compose_test/src/app.py文件。
from flask import Flask
from redis import Redis

app = Flask(__name__)
redis = Redis(host='redis', port=6379)

@app.route('/')
def hello():
    count = redis.incr('hits')
    return 'Hello World! I have been seen {} times.\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
1
2
3
4
5
6
7
8
9
10
11
12
13
3 .创建python 需求文件 compose_test/docker/requirements.txt
flask
redis
1
2
Step 2: 创建容器的Dockerfile文件
一个容器一个Dockerfile文件 
为了工程目录更加清晰，在compose_test/docker/目录下创建compose_test/docker/web目录

$ cd docker
$ mkdir web
1
2
在compose_test/docker/web/目录中创建Dockerfile文件：

FROM python:3.4
ADD . /docker      
ADD ../../src /src  # ADD的源路径应该在Dockerfile文件的上下文环境中（即Dockerfile文件所在根目录及其子目录下，如ADD . /src是合法的，而ADD ../../src /src不合法），此处为了清晰的项目结构，只是展示ADD指令，实际中应该用volumes挂载
WORKDIR /src
RUN pip install -r /docker/requirements.txt
CMD ["python", "app.py"]
1
1
2
3
4
5
Dockerfile文件告诉docker了如下信息：

从Python 3.4镜像开始构建一个容器镜像。 

复杂当前 . （即compose_test/docker）目录到容器的/docker目录。

复制../../src目录（即compose_test/src） 到容器中的/src目录（此处../../src是不合法的路径，ADD的源路径必须在Dockerfile文件的上下文环境中，即Dockerfile文件所在根目录或起子目录中，实际应该使用volumes挂载项目源文件）。 

将容器的工作目录设置为/src。 
安装Python依赖关系。
将容器的默认命令设置为python app.py。

Step 3: 定义docker-compose脚本
在compose_test/docker/目录下创建docker-compose.yml文件，并在里面定义服务，内容如下：

version: '3'
services:
  web:
    build: ./web/
    ports:
     - "5000:5000"
  redis:
    image: "redis:3.0.7"
1
2
3
4
5
6
7
8
这个compose文件定义了两个服务，即定义了web和redis两个容器。 
web容器： 
* 使用当前的web目录（compose_test/docker/web/）中的Dockerfile构建映像。 
* 将容器上的暴露端口5000映射到主机上的端口5000。 我们使用Flask Web服务器的默认端口5000。 
  redis容器： 
* redis服务使用从Docker Hub提取的官方redis镜像3.0.7版本。

Step 4: 使用Compose构建并运行您的应用程序
在compose_test/docker/目录下执行docker-compose.yml文件：

$ docker-compose up
# 若是要后台运行： $ docker-compose up -d
# 若不使用默认的docker-compose.yml 文件名：
$ docker-compose -f server.yml up -d 
1
2
3
4
然后在浏览器中输入http://0.0.0.0:5000/查看运行的应用程序。

Step 5: 编辑compose文件以添加文件绑定挂载
上面的代码是在构建时静态复制到容器中的，即通过Dockerfile文件中的ADD ./src /src命令实现物理主机中的源码复制到容器中，这样在后续物理主机src目录中代码的更改不会反应到容器中。 
可以通过volumes 关键字实现物理主机目录挂载到容器中的功能（同时删除Dockerfile中的ADD指令，不需要创建镜像时将代码打包进镜像，而是通过volums动态挂载，容器和物理host共享数据卷）：

version: '3'
services:
  web:
    build: .
    ports:
     - "5000:5000"
    volumes:
     - ../src :/src
  redis:
    image: "redis:3.0.7"
1
2
3
4
5
6
7
8
9
10
通过volumes（卷）将主机上的项目目录（compose_test/src）挂载到容器中的/src目录下，允许您即时修改代码，而无需重新构建映像。

Step 6: 重新构建和运行应用程序
使用更新的compose文件构建应用程序，然后运行它。

$ docker-compose up -d
1
6.compose常用服务配置参考
Compose文件是一个定义服务，网络和卷的YAML文件。 Compose文件的默认文件名为docker-compose.yml。

**提示：您可以对此文件使用.yml或.yaml扩展名。 他们都工作。

与docker运行一样，默认情况下，Dockerfile中指定的选项（例如，CMD，EXPOSE，VOLUME，ENV）都被遵守，你不需要在docker-compose.yml中再次指定它们。

同时你可以使用类似Bash的$ {VARIABLE} 语法在配置值中使用环境变量，有关详细信息，请参阅变量替换。

本节包含版本3中服务定义支持的所有配置选项。

build
build 可以指定包含构建上下文的路径：

version: '2'
services:
  webapp:
    build: ./dir
1
2
3
4
或者，作为一个对象，该对象具有上下文路径和指定的Dockerfile文件以及args参数值：

version: '2'
services:
  webapp:
    build:
      context: ./dir
      dockerfile: Dockerfile-alternate
      args:
        buildno: 1
1
2
3
4
5
6
7
8
webapp服务将会通过./dir目录下的Dockerfile-alternate文件构建容器镜像。 
如果你同时指定image和build，则compose会通过build指定的目录构建容器镜像，而构建的镜像名为image中指定的镜像名和标签。

build: ./dir
image: webapp:tag
1
2
这将由./dir构建的名为webapp和标记为tag的镜像。

context
包含Dockerfile文件的目录路径，或者是git仓库的URL。 
当提供的值是相对路径时，它被解释为相对于当前compose文件的位置。 该目录也是发送到Docker守护程序构建镜像的上下文。

dockerfile
备用Docker文件。Compose将使用备用文件来构建。 还必须指定构建路径。

args
添加构建镜像的参数，环境变量只能在构建过程中访问。 
首先，在Dockerfile中指定要使用的参数：

ARG buildno
ARG password

RUN echo "Build number: $buildno"
RUN script-requiring-password.sh "$password"
1
2
3
4
5
然后在args键下指定参数。 你可以传递映射或列表：

build:
  context: .
  args:
    buildno: 1
    password: secret

build:
  context: .
  args:
    - buildno=1
    - password=secret
1
2
3
4
5
6
7
8
9
10
11
** 注意：YAML布尔值（true，false，yes，no，on，off）必须用引号括起来，以便解析器将它们解释为字符串。

image
指定启动容器的镜像，可以是镜像仓库/标签或者镜像id（或者id的前一部分）

image: redis
image: ubuntu:14.04
image: tutum/influxdb
image: example-registry.com:4000/postgresql
image: a4bc65fd
1
2
3
4
5
如果镜像不存在，Compose将尝试从官方镜像仓库将其pull下来，如果你还指定了build，在这种情况下，它将使用指定的build选项构建它，并使用image指定的名字和标记对其进行标记。

container_name
指定一个自定义容器名称，而不是生成的默认名称。

container_name: my-web-container
1
由于Docker容器名称必须是唯一的，因此如果指定了自定义名称，则无法将服务扩展到多个容器。

volumes
卷挂载路径设置。可以设置宿主机路径 （HOST:CONTAINER） 或加上访问模式 （HOST:CONTAINER:ro）,挂载数据卷的默认权限是读写（rw），可以通过ro指定为只读。 
你可以在主机上挂载相对路径，该路径将相对于当前正在使用的Compose配置文件的目录进行扩展。 相对路径应始终以 . 或者 .. 开始。

volumes:
  # 只需指定一个路径，让引擎创建一个卷
  - /var/lib/mysql
  # 指定绝对路径映射
  - /opt/data:/var/lib/mysql
  # 相对于当前compose文件的相对路径
  - ./cache:/tmp/cache
  # 用户家目录相对路径
  - ~/configs:/etc/configs/:ro
  # 命名卷
  - datavolume:/var/lib/mysql
    1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    11
    12
    13
    14
    15
    但是，如果要跨多个服务并重用挂载卷，请在顶级volumes关键字中命名挂在卷，但是并不强制，如下的示例亦有重用挂载卷的功能，但是不提倡。

version: "3"

services:
  web1:
    build: ./web/
    volumes:
      - ../code:/opt/web/code
  web2:
    build: ./web/
    volumes:
      - ../code:/opt/web/code
1
2
3
4
5
6
7
8
9
10
11
12
** 注意：通过顶级volumes定义一个挂载卷，并从每个服务的卷列表中引用它， 这会替换早期版本的Compose文件格式中volumes_from。

version: "3"

services:
  db:
    image: db
    volumes:
      - data-volume:/var/lib/db
  backup:
    image: backup-service
    volumes:
      - data-volume:/var/lib/backup/data

volumes:
  data-volume:
1
2
3
4
5
6
7
8
9
10
11
12
13
14
command
覆盖容器启动后默认执行的命令。

command: bundle exec thin -p 3000
1
2
该命令也可以是一个类似于dockerfile的列表：

command: ["bundle", "exec", "thin", "-p", "3000"]
1
2
links
链接到另一个服务中的容器。 请指定服务名称和链接别名（SERVICE：ALIAS），或者仅指定服务名称。

web:
  links:
   - db
   - db:database
   - redis

    1
    2
    3
    4
    5
    在当前的web服务的容器中可以通过链接的db服务的别名database访问db容器中的数据库应用，如果没有指定别名，则可直接使用服务名访问。

链接不需要启用服务进行通信 - 默认情况下，任何服务都可以以该服务的名称到达任何其他服务。 （实际是通过设置/etc/hosts的域名解析，从而实现容器间的通信。故可以像在应用中使用localhost一样使用服务的别名链接其他容器的服务，前提是多个服务容器在一个网络中可路由联通）

links也可以起到和depends_on相似的功能，即定义服务之间的依赖关系，从而确定服务启动的顺序。

external_links
链接到docker-compose.yml 外部的容器，甚至并非 Compose 管理的容器。参数格式跟 links 类似。

external_links:
 - redis_1
 - project_db_1:mysql
 - project_db_1:postgresql
    1
    2
    3
    4
    expose
    暴露端口，但不映射到宿主机，只被连接的服务访问。 
    仅可以指定内部端口为参数

expose:
 - "3000"
 - "8000"
    1
    2
    3
    ports
    暴露端口信息。 
    常用的简单格式：使用宿主：容器 （HOST:CONTAINER）格式或者仅仅指定容器的端口（宿主将会随机选择端口）都可以。

** 注意：当使用 HOST:CONTAINER 格式来映射端口时，如果你使用的容器端口小于 60 你可能会得到错误得结果，因为 YAML 将会解析 xx:yy 这种数字格式为 60 进制。所以建议采用字符串格式。

简单的短格式：

ports:
 - "3000"
 - "3000-3005"
 - "8000:8000"
 - "9090-9091:8080-8081"
 - "49100:22"
 - "127.0.0.1:8001:8001"
 - "127.0.0.1:5000-5010:5000-5010"
 - "6060:6060/udp"
    1
    2
    3
    4
    5
    6
    7
    8
    9
    在v3.2中ports的长格式的语法允许配置不能用短格式表示的附加字段。 
    长格式：

ports:
  - target: 80
    published: 8080
    protocol: tcp
    mode: host
    1
    2
    3
    4
    5
    target：容器内的端口 
    published：物理主机的端口 
    protocol：端口协议（tcp或udp） 
    mode：host 和ingress 两总模式，host用于在每个节点上发布主机端口，ingress 用于被负载平衡的swarm模式端口。

restart
no是默认的重启策略，在任何情况下都不会重启容器。 指定为always时，容器总是重新启动。 如果退出代码指示出现故障错误，则on-failure将重新启动容器。

restart: "no"
restart: always
restart: on-failure
restart: unless-stopped
1
2
3
4
environment
添加环境变量。 你可以使用数组或字典两种形式。 任何布尔值; true，false，yes，no需要用引号括起来，以确保它们不被YML解析器转换为True或False。 
只给定名称的变量会自动获取它在 Compose 主机上的值，可以用来防止泄露不必要的数据。

environment:
  RACK_ENV: development
  SHOW: 'true'
  SESSION_SECRET:

environment:
  - RACK_ENV=development
  - SHOW=true
  - SESSION_SECRET
    1
    2
    3
    4
    5
    6
    7
    8
    9
    ** 注意：如果你的服务指定了build选项，那么在构建过程中通过environment定义的环境变量将不会起作用。 将使用build的args子选项来定义构建时的环境变量。

pid
将PID模式设置为主机PID模式。 这就打开了容器与主机操作系统之间的共享PID地址空间。 使用此标志启动的容器将能够访问和操作裸机的命名空间中的其他容器，反之亦然。即打开该选项的容器可以相互通过进程 ID 来访问和操作。

pid: "host"
1
dns
配置 DNS 服务器。可以是一个值，也可以是一个列表。

dns: 8.8.8.8
dns:
  - 8.8.8.8
  - 9.9.9.9

---------------------
