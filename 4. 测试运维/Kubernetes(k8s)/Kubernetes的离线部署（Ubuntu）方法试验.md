为了在内网上使用Kubernetes，有时候因为网络原因，需要离线进行部署。



我这里使用Ubuntu进行试验，需要解决几个问题：

1. **Ubuntu的离线安装**。下载，安装时选择不联网可以正常完成，没有问题。

2. Ubuntu的包更新

   ，应用系统包必须更新到较新版本，否则有的软件安装不上。

   - 这个可以通过建立本地内网的Archive镜像来实现。
     - 这个其实嘛，最好是全部抓源码自己编译，搞个分发版。不过难度和资源都比较大，还是吃现成的吧。
     - 先镜像下来整个最新的软件包。参考：
       - 创建Ubuntu安装包服务镜像的脚本，https://my.oschina.net/u/2306127/blog/1622218
       - 建立Ubuntu apt的本地Mirror，https://my.oschina.net/u/2306127/blog/541895
       - 使用容器技术搭建apt镜像站，https://my.oschina.net/u/2306127/blog/1626991
       - 使用IPFS搭建Ubuntu apt镜像站点，https://my.oschina.net/u/2306127/blog/1626254
     - 再把archive包目录全部复制到内网（物理拷贝或通过网闸），然后建立mirror服务。
       - 使用apt-mirror或者nginx、ipfs建立静态网站服务即可。
   - 修改/etc/apt/sources.list或该目录下相关文件，指向到新建立的服务。
     - 然后运行 sudo apt update && sudo apt upgrade -y即可更新安装包了。

3. Docker容器引擎的下载和离线安装

   ，这是Kubernetes运行的基础了。

   - 如果使用GPU，还需要下载Nvidia-Docker并进行安装。

4. **kubeadm/kubectl/kubelet的安装**，这是Kubernetes运行的几个基础文件。其它的部分都已经容器化了，通过容器的离线下载进行部署。

5. **Kubernetes系统容器的下载和安装**

   。需要几个步骤来完成。

   - 通过脚本下载（需要周转的哈）到本地（参考 [Ubuntu 18.04 LTS安装Kubernetes 1.11](https://my.oschina.net/u/2306127/blog/1922542)）。
   - 然后export到容器档案格式。
   - 复制到内部网络环境。
   - 对容器档案解包，恢复为Docker镜像。

6. **使用kubeadm或其它工具进行安装**

   。与在线安装基本相同，需要指定安装的版本，否则会去在线搜索。

   - 参考 [Ubuntu 18.04 LTS安装Kubernetes 1.11](https://my.oschina.net/u/2306127/blog/1922542)。



还有其它的办法（满足的需求和效果不同），包括：

- 在网络环境下，做好虚拟机，然后整个放到内网上运行。但是，更新还需要单独处理，而且需要注意软件和数据的安全性。
- 使用DinD（Docker in Docker），跟虚拟机类似，不同的是整个Kubernetes集群都运行在Docker中。
- 建立自己的操作系统分发版，把相关软件打包进去。



- 用kubeadm离线部署kubernetes v1.9.0，https://blog.csdn.net/u012286287/article/details/79716588
- Kubernetes-基于Rancher进行Kubernetes的离线安装，https://www.kubernetes.org.cn/4089.html
- 推荐，使用的centos：[[K8s 1.9实践\]Kubeadm 1.9 HA 高可用 集群 本地离线镜像部署](https://www.kubernetes.org.cn/3536.html)