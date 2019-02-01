前面介绍了Docker三剑客中的两个，今天我们介绍一下三剑客中的最后一个——docker-compose。接下来的内容，我们还是从五个方面来讲解，主要包括如下：

Compose简介
Compose安装与卸载
Compose常用命令
Compose模板文件
Compose实战
一、Compose简介
1、简介

![compose](https://img-blog.csdn.net/20180702211450759?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Compose项目是Docker官方的开源项目，负责实现对Docker容器集群的快速编排。它是一个定义和运行多容器的docker应用工具。使用compose，你能通过YMAL文件配置你自己的服务，然后通过一个命令，你能使用配置文件创建和运行所有的服务。

Compose是一个定位“定义和运行多个Docker容器应用的工具”，其前身是Fig，目前使用的Compose仍然兼容Fig格式的模板文件。

Compose的代码主要使用Python编写，其开源地址为：https://github.com/docker/compose。

我们知道在Docker中构建自定义的镜像是通过使用Dockerfile模板文件来实现的，从而可以让用户很方便定义一个单独的应用容器。而Compose使用的模板文件就是一个YAML格式文件，它允许用户通过一个docker-compose.yml来定义一组相关联的应用容器为一个项目(project)。

注意：Fig时代支持的配置文件名为fig.yml以及fig.yaml；为了兼容遗留的Fig化配置，目前Compose支持的配置文件类型非常丰富，主要有以下几种：fig.yaml、docker-compose.yml、docker-compose.yaml以及用户指定的配置文件路径。（可通过环境变量COMPOSE_FILE或-f参数自定义配置文件）

在Compose中有两个重要的概念：

服务（service）：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。
项目（project）：由一组关联的应用容器组成的一个完成业务单元，在docker-compose.yml中定义。
以上可以理解为： 
服务（service）就是在它下面可以定义应用需要的一些服务，代表配置文件中的每一项服务。每个服务都有自己的名字、使用的镜像、挂载的数据卷、所属的网络、依赖哪些其他服务等等，即以容器为粒度，用户需要Compose所完成的任务。

项目（project）代表用户需要完成的一个项目，即是Compose的一个配置文件可以解析为一个项目，即Compose通过分析指定配置文件，得出配置文件所需完成的所有容器管理与部署操作。

Compose的默认管理对象时项目，通过子命令对项目中的一组容器进行便捷地生命周期管理。

二、Compose安装与卸载
1、Compose安装
Compose目前已经完全支持Linux、Mac OS和Windows，在我们安装Compose之前，需要先安装Docker，对于Mac OS和Windows的安装比较容易，可以参考Install Docker for Mac和Install Docker for Windows，对于Linux的安装可以参考Ubuntu下docker安装及简单应用。

（1）、Linux平台的安装
这里是使用了官方编译好的二进制包，地址：https://github.com/docker/compose/releases。把这些二进制文件下载后直接放到执行路径下面，并添加权限即可。

curl -L https://github.com/docker/compose/releases/download/1.22.0-rc1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

然后我们执行docker-compose -version可以查看到具体的信息。

![docker-compose](https://img-blog.csdn.net/20180707093147558?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 

（2）、Mac OS 系统 & Windows系统安装
对于Mac和windows用户来说，Docker for Mac 、Docker for Windows 和 Docker Toolbox 早已经集成了docker-compose，所以用户不需要分别再安装docker-compose了。

（3）、其他可选方式安装
因为Compose是Python编写的，我们可以将其当做一个Python应用从pip源中安装。

如果是使用virtualenv环境，则执行如下命令：

pip install docker-compose
1
当在本地环境安装，则执行：

sudo pip install docker-compose
1
2、Compose卸载
如果是二进制包方式安装的，删除二进制文件即可：

sudo rm /usr/local/bin/docker-compose
1
如果是通过Python pip工具安装的，则执行如下命令删除：

sudo pip uninstall docker-compose
1
三、Compose常用命令
在我们使用Compose前，可以通过执行docker-compose --help|-h来查看Compose基本命令用法。

![docker-compose](https://img-blog.csdn.net/20180703225125244?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们也可以通过执行docker-compose [COMMAND] --help或者docker-compose --help [COMMAND]来查看某个具体的使用格式。

从上面的提示中我们可以知道Compose命令的基本的使用格式为：

docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
1
命令选项如下：

-f，–file FILE指定使用的Compose模板文件，默认为docker-compose.yml，可以多次指定。
-p，–project-name NAME指定项目名称，默认将使用所在目录名称作为项目名。
-x-network-driver 使用Docker的可拔插网络后端特性（需要Docker 1.9 及以后版本）
-x-network-driver DRIVER指定网络后端的驱动，默认为bridge（需要Docker 1.9 及以后版本）
-verbose输出更多调试信息
-v，–version打印版本并退出
Docker Compose常用命令列表如下：

命令	说明
build	构建项目中的服务容器
help	获得一个命令的帮助
kill	通过发送SIGKILL信号来强制停止服务容器
config	验证和查看compose文件配置
create	为服务创建容器。只是单纯的create，还需要使用start启动compose
down	停止并删除容器，网络，镜像和数据卷
exec	在运行的容器中执行一个命令
logs	查看服务容器的输出
pause	暂停一个服务容器
port	打印某个容器端口所映射的公共端口
ps	列出项目中目前的所有容器
pull	拉取服务依赖的镜像
push	推送服务镜像
restart	重启项目中的服务
rm	删除所有（停止状态的）服务容器
run	在指定服务上执行一个命令
scale	设置指定服务运行的容器个数
start	启动已经存在的服务容器
stop	停止已经处于运行状态的容器，但不删除它
top	显示运行的进程
unpause	恢复处于暂停状态中的服务
up	自动完成包括构建镜像、创建服务、启动服务并关闭关联服务相关容器的一些列操作
version	打印版本信息
1、build
格式为：

docker-compose build [options] [--build-arg key=val...] [SERVICE...]
1
构建（重新构建）项目中的服务容器。

选项包括：

–compress 通过gzip压缩构建上下环境
–force-rm 删除构建过程中的临时容器
–no-cache 构建镜像过程中不使用缓存
–pull 始终尝试通过拉取操作来获取更新版本的镜像
-m, –memory MEM 为构建的容器设置内存大小
–build-arg key=val 为服务设置build-time变量
2、help
获得一个命令的帮助。

3、kill
格式为：

docker-compose kill [options] [SERVICE...]
1
通过发送SIGKILL信号来强制停止服务容器。 
支持通过-s参数来指定发送的信号，例如通过如下指令发送SIGINT信号：

docker-compose kill -s SIGINT
1
4、config
格式为：

docker-compose config [options]
1
验证并查看compose文件配置。

选项包括：

–resolve-image-digests 将镜像标签标记为摘要
-q, –quiet 只验证配置，不输出。 当配置正确时，不输出任何内容，当文件配置错误，输出错误信息
–services 打印服务名，一行一个
–volumes 打印数据卷名，一行一个
5、create
格式为：

docker-compose create [options] [SERVICE...]
1
为服务创建容器.只是单纯的create，还需要使用start启动compose。

选项包括：

–force-recreate 重新创建容器，即使它的配置和镜像没有改变，不兼容–no-recreate参数
–no-recreate 如果容器已经存在，不需要重新创建. 不兼容–force-recreate参数
–no-build 不创建镜像，即使缺失
–build 创建容器前，生成镜像
6、down
格式为：

docker-compose down [options]
1
停止和删除容器、网络、卷、镜像，这些内容是通过docker-compose up命令创建的. 默认值删除 容器 网络，可以通过指定 rmi 、volumes参数删除镜像和卷。

选项包括：

–rmi type 删除镜像，类型必须是: ‘all’: 删除compose文件中定义的所以镜像；’local’: 删除镜像名为空的镜像
-v, –volumes 删除已经在compose文件中定义的和匿名的附在容器上的数据卷
–remove-orphans 删除服务中没有在compose中定义的容器
7、exec
格式为：

docker exec [options] SERVICE COMMAND [ARGS...]
1
与docker exec命令功能相同，可以通过service name登陆到容器中。

选项包括：

-d 分离模式，后台运行命令.
–privileged 获取特权.
–user USER 指定运行的用户.
-T 禁用分配TTY. By default docker-compose exec分配 a TTY.
–index=index 当一个服务拥有多个容器时，可通过该参数登陆到该服务下的任何服务，例如：docker-compose exec –index=1 web /bin/bash ，web服务中包含多个容器
8、logs
格式为：

docker-compose logs [options] [SERVICE...]
1
查看服务容器的输出。默认情况下，docker-compose将对不同的服务输出使用不同的颜色来区分。可以通过–no-color来关闭颜色。

9、pause
格式为：

docker-compose pause [SERVICE...]
1
暂停一个服务容器。

10、port
格式为：

docker-compose port [options] SERVICE PRIVATE_PORT
1
显示某个容器端口所映射的公共端口。

选项包括：

–protocol=proto 指定端口协议，TCP（默认值）或者UDP
–index=index 如果同意服务存在多个容器，指定命令对象容器的序号（默认为1）
11、ps
格式为：

docker-compose ps [options] [SERVICE...]
1
列出项目中目前的所有容器。

选项包括：

-q 只打印容器的ID信息
12、pull
格式为：

docker-compose pull [options] [SERVICE...]
1
拉取服务依赖的镜像。

选项包括：

–ignore-pull-failures 忽略拉取镜像过程中的错误
–parallel 多个镜像同时拉取
–quiet 拉取镜像过程中不打印进度信息
13、push
格式为：

docker-compose push [options] [SERVICE...]
1
推送服务依的镜像。

选项包括：

–ignore-push-failures 忽略推送镜像过程中的错误
14、restart
格式为：

docker-compose restart [options] [SERVICE...]
1
重启项目中的服务。

选项包括：

-t, –timeout TIMEOUT 指定重启前停止容器的超时（默认为10秒）
15、rm
格式为：

docker-compose rm [options] [SERVICE...]
1
删除所有（停止状态的）服务容器。

选项包括：

–f, –force 强制直接删除，包括非停止状态的容器
-v 删除容器所挂载的数据卷
16、run
格式为：

docker-compose run [options] [-v VOLUME...] [-p PORT...] [-e KEY=VAL...] SERVICE [COMMAND] [ARGS...]
1
在指定服务上执行一个命令。

例如：

docker-compose run ubuntu ping www.anumbrella.net
1
将会执行一个ubuntu容器，并执行ping www.anumbrella.net命令。

默认情况下，如果存在关联，则所有关联的服务将会自动被启动，除非这些服务已经在运行中。该命令类似于启动容器后运行指定的命令，相关卷、链接等都会按照配置自动创建。有两个不同点：

给定命令将会覆盖原有的自动运行命令
不会自动创建端口，以避免冲突
如果不希望自动启动关联的容器，可以使用–no-deps选项，例如：

docker-compose run --no-deps web
1
将不会启动web容器关联的其他容器。

选项包括：

-d 在后台运行服务容器
–name NAME 为容器指定一个名字
–entrypoint CMD 覆盖默认的容器启动指令
-e KEY=VAL 设置环境变量值，可多次使用选项来设置多个环境变量
-u, –user=”” 指定运行容器的用户名或者uid
–no-deps 不自动启动管理的服务容器
–rm 运行命令后自动删除容器，d模式下将忽略
-p, –publish=[] 映射容器端口到本地主机
–service-ports 配置服务端口并映射到本地主机
-v, –volume=[] 绑定一个数据卷，默认为空
-T 不分配伪tty，意味着依赖tty的指令将无法运行
-w, –workdir=”” 为容器指定默认工作目录
17、scale
格式为：

docker-compose scale [options] [SERVICE=NUM...]
1
设置指定服务运行的容器个数。 
通过service=num的参数来设置数量。例如：

docker-compose scale web=3 db=2
1
将启动3个容器运行web服务，2个容器运行db服务。一般情况下，当指定书目多于该服务当前实际运行容器，将新创建并启动容器；反之，将停止容器。

选项包括：

-t, –timeout TIMEOUT 停止容器时候的超时（默认为10秒）
18、start
格式为：

docker-compose start [SERVICE...]
1
启动已经存在的服务容器。

19、stop
格式为：

docker-compose stop [options] [SERVICE...]
1
停止已经处于运行状态的容器，但不删除它。

选项包括：

-t, –timeout TIMEOUT 停止容器时候的超时（默认为10秒）
20、top
格式为：

docker-compose stop [options] [SERVICE...]
1
显示各个容器运行的进程情况。

21、unpause
格式为：

docker-compose unpause [SERVICE...]
1
恢复处于暂停状态中的服务。

22、up
格式为：

docker-compose up [options] [--scale SERVICE=NUM...] [SERVICE...]
1
up命令十分强大，它尝试自动完成包括构建镜像，（重新）创建服务，启动服务，并关联服务相关容器的一些列操作。链接的服务都将会被自动启动，除非已经处于运行状态。

多数情况下我们可以直接通过该命令来启动一个项目。

选项包括：

-d 在后台运行服务容器
–no-color 不使用颜色来区分不同的服务的控制输出
–no-deps 不启动服务所链接的容器
–force-recreate 强制重新创建容器，不能与–no-recreate同时使用
–no-recreate 如果容器已经存在，则不重新创建，不能与–force-recreate同时使用
–no-build 不自动构建缺失的服务镜像
–build 在启动容器前构建服务镜像
–abort-on-container-exit 停止所有容器，如果任何一个容器被停止，不能与-d同时使用
-t, –timeout TIMEOUT 停止容器时候的超时（默认为10秒）
–remove-orphans 删除服务中没有在compose文件中定义的容器
–scale SERVICE=NUM 设置服务运行容器的个数，将覆盖在compose中通过scale指定的参数
23、version
格式为：

docker-compose version
1
打印版本信息。

四、Compose模板文件
模板文件是使用Compose的核心，涉及的指令关键字也比较多，大部分指令与docker run相关参数的含义都是类似的。

默认的模板文件迷城为docker-compose.yml，格式为YAML格式。

比如一个Compose模板文件：

version: "2"
services:
    web:
        images: nginx
        ports:
            - "80:80"
        volumes:
            - "/data"
#volumes:

#networks:
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
Docker Compose的模板文件主要分为3个区域，为：

services
服务，在它下面可以定义应用需要的一些服务，每个服务都有自己的名字、使用的镜像、挂载的数据卷、所属的网络、依赖哪些其他服务等等。

volumes
应用的网络，在它下面可以定义应用的名字、使用的网络类型等等。

networks
数据卷，在它下面可以定义的数据卷（名字等等），然后挂载到不同的服务下去使用。

注意：每个服务都必须通过image指令指定镜像或build指令（需要Dockerfile）等来自动构建生成镜像。如果使用build指令，在Dockefile中设置的选项（例如：CMD、EXPOSE、VOLUME、ENV等）将会自动被获取，无需在docker-compose.yml中再次设置。

Docker Compose常用模板文件主要命令：

指令	功能
build	指定服务镜像Dockerfile所在路径
cap_add，cap_drop	指定容器的内核能力（capacity）分配
command	覆盖容器启动后默认执行的命令
cgroup_parent	指定父cgroup组，意味着将基础该组的资源限制
container_name	指定容器名称。默认将会使用项目名称服务名称序号这样的格式
devices	指定设置映射关系
dns	自定义DNS服务器。可以是一个值，也可以是一个列表
dns_search	配置DNS搜索域。可以是一个值，也可以是一个列表
dockerfile	指定额外编译镜像的Dockerfile文件，可以通过该指令来指定
env_file	从文件中获取环境变量，可以为单独的文件路径或列表
environment	设置环境变量，可以使用数组或字典两种格式
expose	暴露端口
extends	基于其他模板文件进行扩展
external_links	链接到docker-compose.yml外部的容器，甚至可以是非Compose管理的外部容器
extra_hosts	指定额外的host名称映射信息
image	指定为镜像名称或镜像ID。如果镜像在本地不存在，Compose将会尝试拉取这个镜像
labels	指定服务镜像Dockerfile所在路径
links	链接到其他服务中的容器
log_driver	指定日志驱动类型，类似于Docker中的–log-driver参数。目前支持三种日志驱动类型：log_driver:”json-file”、log_driver:”syslog”、log_driver:”none”
log_opt	日志驱动的相关参数
net	设置网络模式。参数类似于docker clinet的–net参数一样
pid	跟主机系统共享进程命名空间。打开该选项的容器之间，以及容器和宿主机系统之间可以通过进程ID来相互访问和操作
ports	暴露端口信息
security_opt	指定容器模板标签（label）机制的默认属性（如用户、角色、类型、级别等）
ulimits	指定容器的ulimits限制值
volumes	数据卷所挂载路径设置。可以设置宿主机路径（HOST:CONTAINER）或加上访问模式（HOST:CONTAINER:ro）
volumes_driver	较新版本的Docker支持数据卷的插件驱动
volumes_from	从另一个服务或容器挂载它的数据卷
下面分别介绍一些主要指令的用法：
1、Build
指定Dockerfile所在文件夹的路径（可以是绝对路径，或者相对docker-compose.yml文件的路径）。Compose将会利用它自动构建这个镜像，然后使用这个镜像：

build: /path/to/build/dir
1
2、Cap_add，Cap_drop
指定容器的内核能力（capacity）分配。

例如，让容器具体所有能力可以指定为：

cap_add:
    - ALL
1
2
去掉NET_ADMIN能力可以指定为：

cap_drop:
    - NET_ADMIN
1
2
3、Command
覆盖容器启动后默认执行的命令：

command: echo "hello world"
1
4、cgroup_parent
例如，创建了一个cgroup组名称为cgroups_1:

cgroup_parent: cgroups_1
1
5、container_name
指定容器名称。默认将会使用“项目名称服务名称序号”这样的格式。例如：

container_name: docker-web-container
1
6、devices
指定设备映射关系，例如：

devices:
    - "/dev/ttyUSB1:/dev/ttyUSB0"
1
2
7、dns
自定义DNS服务器。可以是一个值，也可以是一个列表，例如：

dns：8.8.8.8
dns：
    - 8.8.8.8
    - 9.9.9.9
1
2
3
4
8、dns_search
配置DNS搜索域。可以是一个值，也可以是一个列表，例如：

dns_search：example.com
dns_search：
    - domain1.example.com
    - domain2.example.com
1
2
3
4
9、dockerfile
如果需要，指定额外的编译镜像的Dockerfile文件，可以通过该指定龙指定，例如：

dockerfile: Dockerfile-alternate
1
注意：该指令不能跟image同时使用，否则Compose将不知道根据哪个指令来生成最终的服务镜像。

10、env_file
从文件中获取环境变量，可以为单独的文件路径或列表。

如果通过docker-compose -f FILE 方式来指定Compose模板文件，则env_file中变量的路径会基于模板文件路径。

如果有变量名称与environment指定冲突，则按照惯例，以后者为准：

env_file: .env

env_file:
    - ./common.env
    - ./apps/web.env
    - /opt/secrets.env
1
2
3
4
5
6
7
环境变量文件中每一行必须符合格式，支持#开头的注释行：

# common.ev Set development environment
PROG_ENV=development
1
2
3
11、environment
设置环境变量，可以使用数组或字典两种格式。

只给定名称的变量会自动获取运行Compose主机上对应变量的值，可以用来防止防泄露不必要的数据。例如：

environment: 
    RACK_ENV: development
    SESSION_SECRET:
1
2
3
或者：

environment: 
    - RACK_ENV=development
    - SESSION_SECRET
1
2
3
注意：如果变量名称或者值中用到true|false，yes|no等表达布尔含义的词汇，最好放到引号里，避免YAML自动解析某些内容为对应的布尔语义：

y|Y|yes|Yes|YES|n|N|no|No|NO|true|True|TRUE|false|False|FALSE|on|On|ON|off|Off|OFF

12、expose
暴露端口，但不映射到宿主机，只允许能被连接的服务访问。仅可以指定内部端口为参数，如下所示：

expose:
    - "3000"
    - "8000"
1
2
3
13、extends
基于其他模板文件进行扩展。例如，我们已经有了一个webapp服务，定义一个基础模板文件为common.yml，如下所示：

# common.yml

webapp:
    build: ./webapp
    environment:
        - DEBUG=false
        - SEND_EMAILS=false
1
2
3
4
5
6
7
8
再编写一个新的development.yml文件，使用common.yml中的webapp服务进行扩展：

# development.yml

web:
    extends:
        file: common.yml
        service: webapp
    ports:
        - "8000:8000"
    links:
        - db
    environment:
        - DEBUG=true
db:
    image: mysql
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
后者会自动继承common.yml中的webapp服务及环境变量定义。

使用extends需要注意以下几点：

要避免出现循环依赖，例如A依赖B，B依赖C，C反过来依赖A的情况
extends不会继承links和volumes_from中定义的容器和数据卷资源
一般情况下，推荐在基础模板中只定义一些可以共享的镜像和环境变量，在扩展模板中具体指定应用变量、链接、数据卷等信息

14、external_links
链接到docker-compose.yml外部的容器，甚至可以是非Compose管理的外部容器。参数格式跟links类似：

external_links:
    - redis_1
    - project_db_1:mysql
    - project_db_1:postgresql
1
2
3
4
15、extra_hosts
类似于Docker中的–add-host参数，指定额外的host名称映射信息，例如：

extra_hosts:
    - “googledns:8.8.8.8”
    - "dockerhub:52.1.157.61"
1
2
3
会在启动后的服务容器中/etc/hosts文件中添加如下两条条目：

8.8.8.8  googledns
52.1.157.61 dockerhub
1
2
16、image
指定为镜像名称或镜像ID。如果镜像本地不存在，Compose将会尝试拉取这个镜像，例如：

image: ubuntu
image: mysql
1
2
17、labels
为容器添加Docker元数据（metadata）信息。例如，可以为容器添加辅助说明信息：

labels：
    com.startupteam.description: "webapp for a strtup team"
1
2
18、links
链接到其他服务中的容器。使用服务名称（同时作为别名），或者“服务名称:服务别名”（如 SERVICE:ALIAS）,这样的格式都可以，例如：

links:
    - db
    - db:database
    - redis
1
2
3
4
使用别名将会自动在服务容器中的/etc/hosts里创建。例如：

172.17.2.186  db
172.17.2.186  database
172.17.2.187  redis
1
2
3
19、log_driver
类似于Docker中的–log-driver参数，指定日志驱动类型。目前支持三种日志驱动类型：

log_driver: "json-file"
log_driver: "syslog"
log_driver: "none"
1
2
3
20、log_opt
日志驱动的相关参数。例如：

log_driver: "syslog"
log_opt: 
    syslog-address: "tcp://192.168.0.42:123"
1
2
3
21、net
设置网络模式。参数类似于docker client的–net参数：

net: "bridge"
net: "none"
net: "host"
1
2
3
22、pid
跟主机系统共享进程命名空间。打开该选项的容器之间，以及容器和宿主机系统之间可以通过进程ID来相互访问和操作：

pid: "host"
1
23、ports
暴露端口信息。

使用“宿主机:容器”（如 “HOST:CONTAINER”）格式，或者仅仅指定容器的端口（宿主机将会随机选择端口）：

ports：
    - “3000”
    - “8000:8000”
    - "8080:22"
1
2
3
4
24、security_opt
指定容器模板标签（label）机制的默认属性（用户、角色、类型、级别等）。例如，配置标签的用户名和角色名：

security_opt:
    - label:user:USER
    - label:role:ROLE
1
2
3
25、ulimits
指定容器的ulimits值限制值。例如，指定最大进程数为65535，指定文件句柄数为2000（软限制，应用可以随时修改，不能超过硬限制）和 4000（系统硬限制，只能root用户提高）。

ulimits:
    nproc: 65535
    nofile:
        soft: 20000
        hard: 40000
1
2
3
4
5
26、volumes
数据卷所挂载路径设置。可以设置宿主机路径（HOST:CONTAINER）或加上访问模式（HOST:CONTAINER:ro）。该指令中路径支持相对路径。例如：

volumes:
    - /var/lib/mysql
    - cache/:/tmp/cache
    - ~/configs:/etc/configs/:ro
1
2
3
4
27、volumes_from
从另一个服务或容器挂载它的数据卷：

volumes_from:
    - service_name
    - container_name
1
2
3
28、其他指令
此外，还有包括cpu_shares、cpuset、domainname、entrypoint、hostname、ipc、mac_address、mem_limit、memswap_limit、privileged、read_only、restart、stdin_open、tty、user、working_dir等指令，基本跟docker-run中对应参数的功能一致。

例如，指定使用CPU核0和核1，只用50%的CPU资源：

cpu_shares: 73
cpuset: 0,1
1
2
指定服务容器启动后执行的命令：

entrypoint: /code/entrypoint.sh
1
指定容器中运行应用的应用名：

user: nginx
1
指定容器中的工作目录：

working_dir: /code
1
指定容器中搜索域名、主机名、mac地址等：

domainname: anumbrella.net
hostname: dev
mac_address: 08-00-27-0C-0A
1
2
3
指定容器：

ipc: host
1
指定容器中内存和内存交换区限制都为1G：

mem_limit: 1g
memswap_limit: 1g
1
2
允许容器中运行一些特权命令：

privileged: true
1
指定容器退出后的重启策略为始终重启。该命令对保持服务始终运行十分有效，在生产环境中推荐配置为always或者
```
unless-stopped:

restart: always
```
以只读模式挂载容器的root文件系统，意味着不能对容器内容进行修改：

`read_only: true`
打开标准输入，可以接受外部输入：

`stdin_open: true`
模拟一个假的远程控制台

`tty: true`
29、读取环境变量
从1.5.0版本开始，Compose模板文件支持动态读取主机的系统环境变量。

例如，下面的Compose文件将从运行它的环境变量中读取变量${MONGO_VERSION}的值，并将其写入执行的命令中：
```
db:
    image: "mongo:${MONGO_VERSION}"
```
如果执行MONGO_VERSION=3.0 docker-compoe up 则会启动一个mongo:3.0镜像的容器；如果执行MONGO_VERSION=2.8 docker-compoe up则会启动一个mongo:2.8镜像的容器

对应Compose模板文件命令，官方也是在不断更新，目前版本已经是第三版了，更多更详细的用法可以参考官方文档 
compose-file。

五、Compose实战
讲了那么多，我们开始结合前面的知识点进行讲解使用Compose。首先新建一个文件夹compose，然后创建文件docker-compose.yml。

![docker-compose.yml](https://img-blog.csdn.net/20180707143401102?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

编辑docker-compose.yml如下：
```yaml
version: '2'
services:
  web1:
    image: nginx
    ports: 
      - "8080:80"
    container_name: "web1"
  web2:
    image: nginx
    ports: 
      - "8081:80"
    container_name: "web2"
#volumes:

#networks:
```
在这里我们声明了服务，并在下面定义了两个容器web1，web2，同时指定镜像为nginx，并指定了name或端口，而网络和数据卷并没有进行声明。

接着我们使用docker-compose up 启动该服务。

![up](https://img-blog.csdn.net/20180707154841684?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们可以查看到命令中，首先创建了一个默认的compose_default的网络，然后创建容器，并「Attaching to …」，将网络应用到服务上。

我们可以查看一下具体的网络，使用docker network ls 如下：

![network](https://img-blog.csdn.net/20180707154828932?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后我们访问接口，可以发现具体的日志；

![è®¿é®](https://img-blog.csdn.net/20180707155339697?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![æ¥å¿ä¿¡æ¯](https://img-blog.csdn.net/20180707155359508?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这里我们启动的服务并不是以守护进程启动了，所以关闭客户端就关闭了服务。如果要一守护进程启动，加上-d参数。如下：

docker-compose up -d
![-d](https://img-blog.csdn.net/20180707155708812?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


接下来我们可以使用一些前文介绍的命令，docker-compose ps查看服务，

![ps](https://img-blog.csdn.net/20180707160219764?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

docker-compose stop [name] 停止服务，docker-compose start [name]启动服务，

![stopãstart](https://img-blog.csdn.net/20180707160353863?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

docker-compose rm [name]删除服务，需要停止服务，否则使用-f参数，与docker rm命令类似

![rm](https://img-blog.csdn.net/20180707160742811?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

注意：这个docker-compose rm不会删除应用的网络和数据卷。查看一下网络，可以看到应用创建的网络

如果要删除数据卷、网络、应用则使用docker-compose down命令。

![down](https://img-blog.csdn.net/20180707161222420?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

docker-compose logs -f [name]查看具体服务的日志。

![logs](https://img-blog.csdn.net/20180707161410717?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

通过docker-compose exec [name] shell可以进入容器内部，例如，进入web1容器内部，使用docker-compose exec web1 /bin/bash命令。

![exec](https://img-blog.csdn.net/20180707161800961?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

现在我们更改docker-compose文件，更改为如下配置：
```yaml
version: '2'
services:
  web1:
    image: nginx
    ports: 
      - "8080:80"
    container_name: "web1"
    networks:
      - dev
  web2:
    image: nginx
    ports: 
      - "8081:80"
    container_name: "web2"
    networks:
      - dev
      - pro
  web3:
    image: nginx
    ports: 
      - "8082:80"
    container_name: "web3"
    networks:
      - pro

networks:
  dev:
    driver: bridge
  pro:
    driver: bridge

#volumes:
```
我们添加了容器web3，并指定了网络。使用docker-compose down关闭先前的服务，重新启动服务。

![up -d](https://img-blog.csdn.net/20180707162835192?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们指定了dev、pro网络，同时给web1指定了dev网络，web2指定了dev、pro网络，web3指定了pro网络，因此web1和web2可以互相访问到，web2和web3可以互相访问到，而web1与web3就无法访问。这就可以通过配置来实现容器直接的互通和隔离。

现在我们再次更改docker-compose.yml模板文件，如下：
```yaml
version: '2'
services:
  web1:
    image: nginx
    ports: 
      - "8080:80"
    container_name: "web1"
    networks:
      - dev
    volumes:
      - ntfs:/data
  web2:
    image: nginx
    ports: 
      - "8081:80"
    container_name: "web2"
    networks:
      - dev
      - pro
    volumes:
      - ./:/usr/share/nginx/html
  web3:
    image: nginx
    ports: 
      - "8082:80"
    container_name: "web3"
    networks:
      - pro
    volumes:
      - ./:/usr/share/nginx/html

networks:
  dev:
    driver: bridge
  pro:
    driver: bridge

volumes:
  ntfs:
    driver: local
```
我们添加了volumes声明，在web1中我们挂载数据在本地，而web2、web3我们则挂载compose当前目录与nginx的/usr/share/nginx/html相通。同样使用docker-compose down关闭先前的服务，并重新启动服务。

然后我们在当前compose目录新建index.html文件，这里的index.html随便编写一些内容，

![index.html](https://img-blog.csdn.net/20180707170850134?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后我们可以进入web2、web3中，在容器的/usr/share/nginx/html中我们可以查看到与我们compose目录一致。

![volumes1](https://img-blog.csdn.net/20180707171117957?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后我们访问http://localhost:8081和http://localhost:8082，得到的结果与我们预期的也一致。如下：

![volumes2](https://img-blog.csdn.net/20180707171716501?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FudW1icmVsbGE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

通过Compose，我们快速创建一套基于Docker容器的服务栈，然后使用docker-compose脚本来启动，停止和重启应用，非常适合组合使用多个容器进行开发的场景。因此掌握Compose的使用也是非常重要的，到此Compose的知识点也介绍完毕，Docker三剑客的知识也到此结束，有什么建议欢迎一起讨论。

参考
https://docs.docker.com/compose/
http://www.ywnds.com/?p=7592
《Docker技术入门与实战 第2版》
