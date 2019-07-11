# 一、Homebrew是什么



![img](https:////upload-images.jianshu.io/upload_images/701177-a36f726a13e92779.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/814/format/webp)

Homebrew

Homebrew是一款Mac OS平台下的软件包管理工具，拥有安装、卸载、更新、查看、搜索等很多实用的功能。简单的一条指令，就可以实现包管理，而不用你关心各种依赖和文件路径的情况，十分方便快捷。

援引[官方](https://link.jianshu.com?t=http%3A%2F%2Fbrew.sh%2F)的一句话：又提示缺少套件啦？别担心，Homebrew 随时守候。Homebrew —— OS X 不可或缺的套件管理器。

# 二、Homebrew安装

## 1. 要求

- Intel CPU

- OS X 10.9 or higher

- Xcode命令行工具

  ```
  $ xcode-select --install
  ```

- 支持shell (sh或者bash)

## 2. 安装和卸载

- 安装

  ```
  /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
  ```

- 卸载

  ```
  $ cd `brew --prefix`
  $ rm -rf Cellar
  $ brew prune
  $ rm `git ls-files`
  $ rm -r Library/Homebrew Library/Aliases Library/Formula Library/Contributions
  $ rm -rf .git
  $ rm -rf ~/Library/Caches/Homebrew
  ```

# 三、Homebrew基本使用

- 安装任意包

  ```
  $ brew install <packageName>
  ```

  示例：安装node

  ```
  $ brew install node
  ```

- 卸载任意包

  ```
  $ brew uninstall <packageName>
  ```

  示例：卸载git

  ```
  $ brew uninstall git
  ```

- 查询可用包

  ```
  $ brew search <packageName>
  ```

- 查看已安装包列表

  ```
  $ brew list
  ```

- 查看任意包信息

  ```
  $ brew info <packageName>
  ```

- 更新Homebrew

  ```
  $ brew update
  ```

- 查看Homebrew版本

  ```
  $ brew -v
  ```

- Homebrew帮助信息

  ```
  $ brew -h
  ```

# 四、注意

在Mac OS X 10.11系统以后，/usr/local/等系统目录下的文件读写是需要系统root权限的，以往的Homebrew安装如果没有指定安装路径，会默认安装在这些需要系统root用户读写权限的目录下，导致有些指令需要添加sudo前缀来执行，比如升级Homebrew需要：

```
$ sudo brew update
```

如果你不想每次都使用sudo指令，你有两种方法可以选择:

1. 对/usr/local 目录下的文件读写进行root用户授权

   ```
   $ sudo chown -R $USER /usr/local
   ```

   示例：

   ```
   $ sudo chown -R gaojun /usr/local
   ```

2. （推荐）安装Homebrew时对安装路径进行指定，直接安装在不需要系统root用户授权就可以自由读写的目录下

   ```
   <install path> -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   ```



作者：manofit

链接：https://www.jianshu.com/p/de6f1d2d37bf

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。