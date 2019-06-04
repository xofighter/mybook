# Linux on Windows(WSL)上使用docker

## 环境

windows 10家庭中文版 1709

## 步骤

### 启用并安装(Ubuntu)Linux on Windows(具体方法请使用搜索引擎)

1. 在“设置->更新和安全->针对开发人员”中使用开发人员模式
2. 开始菜单中搜索“启用或关闭 Windows功能”，找到“适用于Linux的Windows子系统”打上勾
3. 进入应用商店，搜索Ubuntu，找到Ubuntu 18.04并获取
4. 安装好后直接在开始菜单中找到并打开，就可以使用了，如果第一次打开显示installing，那就关掉，等待一会后再开

### 安装Docker for windows或者docker toolbox

> 因为Linux on Windows中无法运行docker的deamon，所以还是需要在windows上安装一个docker

1. 下载Docker for windows(下最新版本，这时候docker是18.06.0-ce)，如果不是win10专业版和企业版而是家庭版那就只能下载docker toolbox，笔者下的是docker toolbox
2. 按照提示安装好之后会有Docker Quickstart Terminal、Kitematic、Oracle VM VirtualBox三个快捷方式，先启动Oracle VM VirtualBox，然后启动Kitematic，根据提示操作(不同版本可能有所不同)，最后会在VM上看到一个名为default的虚拟机
3. 这时候启动Docker Quickstart Terminal就可以直接使用docker了，不过呢，dockerhub的源比较慢，最好换一个，笔者换的是DaoCloud的，步骤如下：
   1. 在Docker Quickstart Terminal命令行执行docker-machine ssh default(default是machine实例的名字，可以更改)进入VM bash
   2. sudo vi /var/lib/boot2docker/profile
   3. 在--label provider=virtualbox的下一行添加--registry-mirror [daocloud加速器地址](http://guide.daocloud.io/dcs/daocloud-services-9152632.html)
   4. 重启docker服务：sudo /etc/init.d/docker restart或者重启VM：exit退出VM bash，在Windows命令行中执行docker-machine restart

### ubuntu更换国内源

> 笔者更换的是清华的源，ubuntu自带的源是美国的，会比较慢 注意，只用于bionic发行版

1. 进入WSL

2. 备份

   ```
   	sudo cp /etc/apt/sources.list /etc/apt/sources.list.old
   ```

3. 打开文件

   ```
   	sudo vim /etc/apt/sources.list
   ```

4. 用以下内容替换

   ```
   	deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic main multiverse restricted universe
   	deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic main multiverse restricted universe
   	deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-security main multiverse restricted universe
   	deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-security main multiverse restricted universe
   	deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-updates main multiverse restricted universe
   	deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-updates main multiverse restricted universe
   	deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-proposed main multiverse restricted universe
   	deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-proposed main multiverse restricted universe
   	deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-backports main multiverse restricted universe
   	deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-backports main multiverse restricted universe
   ```

5. 更新apt

   ```
   	sudo apt update
   ```

> 提供一些镜像地址列表 Debian <https://www.debian.org/mirror/list> Ubuntu <http://wiki.ubuntu.org.cn/源列表>

### 根据[官网](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce)给WSL安装docker

> 这时候是docker18.06.0-ce

### 配置WSL

1. 打开~/目录下的.bashrc文件 vim ~/.bashrc

2. 加入以下内容

   ```
   #配置docker的连接，适用于docker toolbox
   export DOCKER_TLS_VERIFY=1
   export DOCKER_HOST=tcp://192.168.99.100:2376
   export DOCKER_CERT_PATH=/mnt/c/Users/[你的windows系统用户名]/.docker/machine/certs
   
   #因为vm中与windows的共享文件夹目录是/c/Users/，而在Linux on Windows中是/mnt/c/，所以为了docker可以顺利运行(docker客户端会取docker-compose文件的绝对目录作为参数传到服务端使用)，需要把/mnt/c挂载到/c/，不可以用符号链接，因为docker不支持符号链接
   #如果有其他挂载目录，需要自行修改脚本
   if ! mountpoint -q '/c';then
     if [ ! -d "/c" ] && [ ! -f "/c" ]; then
       sudo mkdir -p /c
     fi
     if [ -d "/c" ] && [ ! "$(find / -maxdepth 1 -type l | grep '/c' )" ] && [ ! "$(ls -A /c)" ]; then
       sudo mount --bind /mnt/c /c
     else
       echo "没有挂载/mnt/c到/c"
     fi
   fi
   ```

3. 这时候就可以在Linux on Windows中操作docker了，注意，如果用到本地文件(docker-compose文件或者容器挂载目录等)的话必须在/c目录下操作，就算是/mnt/c都不行