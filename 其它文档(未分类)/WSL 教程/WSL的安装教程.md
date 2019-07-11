@[toc](WSL的安装教程)

-------
> 参考文章：[WSL(Windows Subsystem for Linux)的安装与使用 ](https://www.cnblogs.com/JettTang/p/8186315.html)

# WSL的安装教程

## 介绍
Windows Subsystem for Linux（简称WSL）是一个为在Windows 10上能够原生运行Linux二进制可执行文件（ELF格式）的兼容层。它是由微软与Canonical公司合作开发，目标是使纯正的Ubuntu 14.04 "Trusty Tahr"映像能下载和解压到用户的本地计算机，并且映像内的工具和实用工具能在此子系统上原生运行。

WSL提供了一个微软开发的Linux兼容内核接口（不包含Linux代码），来自Ubuntu的用户模式二进制文件在其上运行。 [4]  
该子系统不能运行所有Linux软件，例如那些图形用户界面，以及那些需要未实现的Linux内核服务的软件。不过，这可以用在外部X服务器上运行的图形X Window系统缓解。
此子系统起源于命运多舛的Astoria项目，其目的是允许Android应用运行在Windows 10 Mobile上。[5]此功能组件从Windows 10 Insider Preview build 14316开始可用。

## 安装

 

1. 管理员权限运行powershell并运行下面的命令：

 

```cmd
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

 

**或者：**控制面板->程序和功能->启用或关闭Windows功能->勾选 适用于Linux的Windows子系统

 

![img](https://images2017.cnblogs.com/blog/723701/201801/723701-20180103223038768-1629438015.png)

 

2.  重启电脑

 

3.  打开应用商城搜索“WSL”，可根据自己需求选择安装一个或多个Linux系统：

 

![img](https://images2017.cnblogs.com/blog/723701/201801/723701-20180103224112299-416738744.png)

 

4.  安装完成后可在开始菜单里找到快捷方式并启动，第一次运行需要等待安装并设置用户名、密码。

 

 ![img](https://images2017.cnblogs.com/blog/723701/201801/723701-20180103224642346-869581243.png)

 

除此以外，在cmd中使用以下几个命令都可以运行WSL：

 

- wsl 或者 bash
- wsl [command]或者bash -c [command]
- Linux系统名称（如：ubuntu），这跟上面在开始菜单里启动是一样的

 

其中，前两种方式会运行默认的linux系统，当你安装多个linux系统之后可以使用wslconfig来指定

 

 

 

### 使用wslconfig命令进行管理

 

1.  设置默认运行的linux系统

 

```
wslconfig /setdefault <DistributionName>
```

 

正如上面所说，如果执行wslconfig /setdefault ubuntu，那么执行bash命令则会运行ubuntu

 

2.  卸载linux系统

 

```
wslconfig /unregister <DistributionName>
```

 

当系统出现问题，我们可以卸载后重新安装。如：wslconfig /unregeister ubuntu

 

3.  查看已安装的linux系统

 

```
wslconfig /list
```


### 设置默认登陆用户

 

当我们运行WSL时，它会用第一次运行时输入的用户名登陆，如果想改为其他用户，可以（以ubuntu为例）：

 

```
ubuntu config --default-user root
```

 

这样，以后再运行ubuntu它就会默认以root登陆。此外还可以“**ubuntu /?**”查看其更多使用方法。

 

### WSL文件系统与本地文件系统互相访问

 

1.  WSL中访问本地文件

 

在“**/mnt**”目录下有“**c**”、“**d**”、“**e**”等文件夹，分别表示本地的**C盘**、**D盘**、**E盘**，直接cd到相应路径下即可。

 

2.  本地访问WSL的根目录

 

微软强烈不建议在外部对WSL文件系统进行更改，所以未公开WSL所在的根目录（不过博主通过某种途

 

径还是找到了→_→）。毕竟有时候我们需要直接读取里面的很多文件（比如说在某个IDE里面设置include path

 

为WSL的/usr/include），如果每次都在WSL里cp命令将其拷贝到本地又太麻烦。

 

下面是其根目录（以ubuntu为例，版本不同可能会有略微差别）：

 

![img](https://images2017.cnblogs.com/blog/723701/201801/723701-20180103225942612-734440618.png)

 

(C:\Users\XXXX\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState\rootfs)

 

不过我**不建议**在外部直接对其文件进行编辑、新建等操作，因为会出现一些问题。

 

 

 

### 设置控制台默认字体

 

WSL字符界面界面默认字体太难看怎么办？注册表大法（以ubuntu为例）：

 

首先打开ubuntu，鼠标右键窗口->属性：

 

![img](https://images2017.cnblogs.com/blog/723701/201801/723701-20180119223254537-1222961305.png)

 

![img](https://images2017.cnblogs.com/blog/723701/201801/723701-20180119223428928-2066925888.png)

 

随便更改一个字体后点确定，此时注册表HKEY_CURRENT_USER/Console下就会出现xxxx_ubuntu.exe一项，

 

我们需要更改（或新建）CodePage和FaceName两个键值，下图设置的Consolas字体：

 

![img](https://images2017.cnblogs.com/blog/723701/201801/723701-20180104102759690-419973019.png)

 

更改完之后，重新打开ubuntu就可以看到效果啦：

 

 ![img](https://images2017.cnblogs.com/blog/723701/201801/723701-20180104104021424-1571579101.png)

 

（注：其实在这里同样可以设置cmd和powershell的默认字体）

## 关于WSL系统的其它操作

这个WSL系统就相当于一个独立的系统，对应系统的设置，软件安装等操作，均可以参考该系统的操作，网上有好多，请自己检索。