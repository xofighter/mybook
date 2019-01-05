# **前言：IntelliJ IDEA**

如果说IntelliJ IDEA是一款现代化智能开发工具的话，Eclipse则称得上是石器时代的东西了。其实笔者也是一枚从Eclipse转IDEA的探索者，随着近期的不断开发实践和调试，逐步体会到这款智能IDE带来的巨大开发便利，在强大的插件功能支持下，诸如对Git和Maven的支持简直让人停不下来，各种代码提示，包括JS更是手到擒来，最终不得不被这款神奇的IDE所折服。为了让身边更多的小伙伴参与进来，决定写下这篇文章，与君共享。(*^_^*)

高级传送门：[IntelliJ IDEA 官网下载 - Ultimate 终极版](https://www.jetbrains.com/idea/download/#section=windows)

激活方法： 安装完成后 选择License  输入 http://intellij.mandroid.cn

# **正文：IntelliJ IDEA 使用教程**

## **1. IDEA VS Eclipse 核心术语比较**

​    **由下图可见：**两者最大的转变就在于工作空间概念的转变，并且在IDEA当中，Project和  Module是作为两个不同的概念，对项目结构是重要意义的，这也恰恰是许多IDEA初学者觉得困扰的地方。

![img](https://upload-images.jianshu.io/upload_images/8069210-1f60e92b9a8d5559.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/648)

###     1.1 为什么要取消工作空间？

​        答： **简单来说，IDEA不需要设置工作空间，因为每一个Project都具备一个工作空间！！**对于每一个IDEA的项目工程（Project）而言，它的每一个子模块（Module）都可以使用独立的JDK和MAVEN。这对于传统项目迈向新项目的重构添加了极大的便利性，这种多元化的灵活性正是Eclipse所缺失的，因为开始Eclipse在初次使用时已经绑死了工作空间。

​    1.2 此外，很多新手都会问，为什么IDEA里面的子工程要称为Module ？

​        答：其实就是模块化的概念，作为聚合工程亦或普通的根目录，它称之为Project，而下面的子工程称为模块，每一个子模块之间可以相关联，也可以没有任何关联。

## **2. 当前项目配置VS 默认配置** 

​    2.1 为什么有了当前项目配置，还需要默认配置呢？

​    **答：**因为IDEA没有工作空间的概念，所以每个新项目（Project）都需要设置自己的JDK和MAVEN等相关配置，这样虽然提高了灵活性，但是却要为每个新项目都要重新配置，这显然不符合我们的预期。在这个背景下，默认配置给予当前项目配置提供了Default选项，问题自然就迎刃而解了。

​    2.2 初始化步骤

​      打开默认配置：顶部导航栏 -> File -> Other Settings -> Default Settings /ProjectStructs 

​      打开当前配置：顶部导航栏 -> File -> Settings / ProjectStructs

​     **示例图：**

![img](https://upload-images.jianshu.io/upload_images/8069210-ac73a71f4046699e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/494)

如果当前项目想覆盖默认配置，直接在Settins/Project Structure设置即可。

##  

> =============================================
>
> 接下来，来看看IDEA如何快速搭建Java开发环境！！
>
> =============================================

## 3. 全局JDK（默认配置）

  具体步骤：顶部工具栏  File ->Other Settins -> Default Project Structure -> SDKs -> JDK

​          示例： 根据下图步骤设置JDK目录，最后点击OK保存。

![img](https://upload-images.jianshu.io/upload_images/8069210-fa02a9132d9aaee2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/683)

PS：同理，当前项目在Project Structure可为工程和各模块设置喜欢的JDK版本。

## 4. 全局Maven（默认配置）

具体步骤：顶部工具栏  File ->Other Settings -> Default Settings -> Build & Tools -> Maven

​      示例： 理论上只要配置了Maven主目录即可，实际开发推荐采用User Settins file .

![img](https://upload-images.jianshu.io/upload_images/8069210-c847845f860324be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

PS:为了方便查阅,推荐在Settings配置好本地仓库. 例如D:\mvnrepository

## 5. 版本控制Git/Svn （默认配置）

具体步骤：顶部工具栏  File ->Other Settings -> Default Settings -> Version Control -> Git

​      示例： IDEA默认集成了对Git/Svn的支持  直接设置执行程序，右边Test提示成功即可。

​                  部分小伙伴反馈说无法找到svn.exe，解决方法：重装SVN，配置项重新选择command line client tools 即可。

![img](https://upload-images.jianshu.io/upload_images/8069210-9bbaa622cf3daeed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

PS: IDEA内置的Git插件灰常好用，尤其是解决冲突性的代码。另外Git客户端推荐SourceTree。

### 6. 自动导包和智能移除 （默认配置）

具体步骤：顶部工具栏  File ->Other Settings -> Default Settings -> Auto Import

​      说明： **在网上看到很多人在提问IDEA为什么不能优化导包而Eclipse可以，**

​              **所以特意抽****出来跟大家分享IDEA如何优化导包。**

![img](https://upload-images.jianshu.io/upload_images/8069210-d66b1318a29ab251.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

##   7. Tomcat Server（当前项目配置） 

很多小伙伴刚开始都找不到Tomcat的配置，其实很简单，Tomcat或者Jetty这些都是部署的容器，自然会联想到Deployment ，打开部署配置，可以看到应用服务器的配置。

配置Tomcat方法： File -> Settings -> Deployment -> Application Servers -> Tomcat Server  

具体配置方法，如下图：

![img](https://upload-images.jianshu.io/upload_images/8069210-4d628299a415e63d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

##  

## IDEA 必备小技能 

为了提升开发效率，撸主贴心为大家准备以下实用指数五颗星的小技巧：

### 8. 自动编译

具体步骤：顶部工具栏  File ->Other Settings -> Default Settings -> Auto Import

说明：开启自动编译之后，结合Ctrl+Shift+F9 会有热更新效果。

![img](https://upload-images.jianshu.io/upload_images/8069210-e10620f9da31fe9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

### 自动编译（Runtime）

具体步骤： 敲击 Ctrl + Shift + Alt + /  然后双击Shift搜索进入Registry ，找到compiler.automake.allow.when.app.running ，然后勾选上。

![img](https://upload-images.jianshu.io/upload_images/8069210-0734034dd7995cf4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/371)

![img](https://upload-images.jianshu.io/upload_images/8069210-3a4d633c2c0496f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

最后 如果想知道SpringBoot如何具体实现热部署的请点击[SpringBoot+IDEA实现热部署教程](https://www.jianshu.com/p/f658fed35786)

### 9. 取消大小写敏感

具体步骤：

File | Settings | Editor | General | Code Completion Case | Sensitive Completion = None

取消大小敏感，在编写代码的时候，代码的自动提示将更加全面和丰富。

![img](https://upload-images.jianshu.io/upload_images/8069210-60b6ee65a7b778a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

### 10. 调整字体类型和字体大小

默认的白色背景和细小的字体会影响大家的编码体验，这里特意提供了调整代码窗的快捷配置。打开配置，搜索Font，然后再Font可以调整字体类型，Size可以调整字体大小，如图：

![img](https://upload-images.jianshu.io/upload_images/8069210-9d5a65fa1d5f809d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

### 10. 将快捷键设置为跟Eclipse一样

很多人可能并不习惯IDEA的快捷键，为了方便，这里我们将快捷键设置为跟 Eclipse一样。

具体步骤: File -> Settings -> Keymap - > 选择Eclipse .

![img](https://upload-images.jianshu.io/upload_images/8069210-de1b7cb998e21a2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

从Eclipse转过来的小伙伴 可以放心使用

### 11. 打开常用工具栏

具体步骤：顶部导航栏 - View -> 勾选 Toolbar & Tool Buttons

如下图所示：

![img](https://upload-images.jianshu.io/upload_images/8069210-575c85658a9b06fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/534)

###  

### 12. 打开Maven神器（强烈推荐！）

 

具体步骤：右侧直接点击 Maven Project 管理插件 ，记得先打开常用工具栏，详见8.3。

如下图所示： 还在Eclipse使用Update命令苦苦挣扎的童鞋，请火速尝试此款插件，能给你带来前所未有的愉快感！！

![img](https://upload-images.jianshu.io/upload_images/8069210-c6aca3ce3f7cb954.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/429)

###  

### 13. 懒人必备快捷键

 

**1. 按【鼠标中键】快速打开智能提示，取代alt+enter 。**

File->Settings-> Keymap-> 搜索 Show Intention Actions -> 添加快捷键为鼠标中键。

**2. 按【F2】快速修改文件名，告别双手操作。**

File->Settings-> Keymap-> 搜索 Rename -> 将快捷键设置为F2 。

**3. 按【F3】直接打开文件所在目录，浏览一步到位。**

File->Settings-> Keymap-> 搜索 Show In Explorer -> 将快捷键设置为F3 。

**4. 按【Ctrl+右键】直接打开实现类，方便开发查询。**

File->Settings-> Keymap-> 搜索 implementation->  Add Mouse Shortcut 将快捷键设置为Ctrl+ 鼠标右键。

 

### **14. 重度强迫症患者**

**1.取消大小写敏感，让自动完成更齐全！**  

File | Settings | Editor | General | Code Completion Case | Sensitive Completion = None。

**2.自动隐藏注释，让源码阅读更为清爽！** 

File -> Settings -> Editor -> General -> Code Folding ->  Documentation comments 勾选。

如何想快速一键打开全部注释，则单击鼠标右键，选择Folding -> Expand Doc comments 。

**3. Maven自动下载源码包，告别反编译，直接上源码注释！！**

File | Settings | Build, Execution, Deployment | Build Tools | Maven | Importing

将Automatically Download  的 Source 勾上。

 

### 15. IDEA十问十答

​    **（1）如何打开本地工程/已存在的工程？**

​         答：点击File -> Open 打开 工程文件夹即可，注意先配置好JDK、Maven等基础配置。

   **（2）IDEA如何删除项目工程？**

​          答：问这个问题的Coder真的好可爱啊哈哈，很肯定的回答你，不需要删，

​              点击File-> Close Project 即可快速关闭当前项目； 示例：

​             什么？你还是想要干掉整个目录？那也阔以，右键Show In Explorer ，删掉文件夹                 即可。不过笔者建议还是直接Close关掉就好啦，万一以后用得上呢，你说呢？

![img](https://upload-images.jianshu.io/upload_images/8069210-8fa3622b2e764134.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/274)

   **（3）如何在单个窗口打开多个Maven工程啊？**

​          答：随便新建一个文件夹，然后将工程都扔进去，使用IDEA打开这个文件夹。

   **（4）如何为当前项目工程添加多个模块啊？**

​         答： 对着工程右键 -> 选择New -> Module -> 通常选择Spring Initializr  ，如图：

![img](https://upload-images.jianshu.io/upload_images/8069210-64822a874d368f32.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/563)

新增模块

![img](https://upload-images.jianshu.io/upload_images/8069210-b1aa086b58e093c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/304)

多模块工程

版权声明

原文链接：[www.jianshu.com/p/9c65b7613c30](https://www.jianshu.com/p/9c65b7613c30)

版权声明： 转载请注明出处 by yizhiwazi