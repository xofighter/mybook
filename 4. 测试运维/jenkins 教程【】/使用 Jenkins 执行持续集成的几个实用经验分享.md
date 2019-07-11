# 使用 Jenkins 执行持续集成的几个实用经验分享

2018年06月09日 21:34:21 [哎_小羊_168](https://me.csdn.net/aixiaoyang168) 阅读数 9342



 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/aixiaoyang168/article/details/80636544

**目录**



### 文章目录

- - [1、 修改 JVM 的内存配置](https://blog.csdn.net/aixiaoyang168/article/details/80636544#1__JVM__6)

  - [2、修改 Jenkins 主目录](https://blog.csdn.net/aixiaoyang168/article/details/80636544#2_Jenkins__26)

  - [3、配置优化减少磁盘空间占用](https://blog.csdn.net/aixiaoyang168/article/details/80636544#3_57)

  - - [3.1、丢弃旧的构建配置](https://blog.csdn.net/aixiaoyang168/article/details/80636544#31_61)
    - [3.2、修改工作空间和构建记录根目录](https://blog.csdn.net/aixiaoyang168/article/details/80636544#32_70)

  - [4、设置全局属性](https://blog.csdn.net/aixiaoyang168/article/details/80636544#4_77)

  - [5、JDK/Maven/Gradle 等软件多版本安装](https://blog.csdn.net/aixiaoyang168/article/details/80636544#5JDKMavenGradle__85)

  - [6、设置构建超时时间](https://blog.csdn.net/aixiaoyang168/article/details/80636544#6_93)

  - [7、配置视图分类管理 Job](https://blog.csdn.net/aixiaoyang168/article/details/80636544#7_Job_98)

  - [8、配置多节点管理](https://blog.csdn.net/aixiaoyang168/article/details/80636544#8_107)

  - [9、一些实用插件](https://blog.csdn.net/aixiaoyang168/article/details/80636544#9_122)

  - - [9.1、Locale 插件控制 Jenkins 页面国际化](https://blog.csdn.net/aixiaoyang168/article/details/80636544#91Locale__Jenkins__126)
    - [9.2、Build Name Setter & Description Setter 插件支持自定义 Build 名称和描述](https://blog.csdn.net/aixiaoyang168/article/details/80636544#92Build_Name_Setter__Description_Setter__Build__135)
    - [9.3、Managed Script 插件管理脚本文件](https://blog.csdn.net/aixiaoyang168/article/details/80636544#93Managed_Script__143)
    - [9.4、PostBuildScript 插件根据 Build 状态执行脚本](https://blog.csdn.net/aixiaoyang168/article/details/80636544#94PostBuildScript__Build__158)
    - [9.5、Jenkins2.0 Pipeline 插件执行持续集成发布流程](https://blog.csdn.net/aixiaoyang168/article/details/80636544#95Jenkins20_Pipeline__163)
    - [9.6、Kubernetes Plugin 插件动态创建 Jenkins Slave](https://blog.csdn.net/aixiaoyang168/article/details/80636544#96Kubernetes_Plugin__Jenkins_Slave_167)

  - [10、JAVA 代码触发 Jenkins Job 创建、删除、停止等操作。](https://blog.csdn.net/aixiaoyang168/article/details/80636544#10JAVA__Jenkins_Job__171)



> 众所周知，持续构建与发布是我们日常工作中要面对的的一个重要环节，目前很多公司都采用 Jenkins 来搭建符合需求的 CI/CD 流程，作为一个持续集成的开源工具，它以安装启动方便，配置简单，上手容易的特点，深受广大用户的欢迎，通过笔者这几年在公司使用 Jenkins 集群，来完成公司日常各项目组持续集成和发布流程的经验，给大家分享几个实用的经验。

## 1、 修改 JVM 的内存配置

Jenkins 启动方式有两种方式，一种是以 Jdk Jar 方式运行，一种是将 War 包放在 Tomcat 容器下运行。不管何种方式运行，都会存在一个问题就是，默认 JVM 内存分配太少，导致启动或者运行一段时间后内存溢出报错 `java.lang.OutOfMemoryError: PermGen space`。所以，需要在启动前修改 JVM 内存配置。以 Tomcat 容器方式启动 Jenkins 为例配置如下：

```
# 进入到 Jenkins 运行所在 Tomcat conf 目录
$ vim catalina.sh
# 在 #JAVA_OPTS="$JAVA_OPTS -Dorg.apache.catalina.security.SecurityListener.UMASK=`umask`" 行下增加修改配置 JVM 内存配置大小，例如下边配置：
JAVA_OPTS="$JAVA_OPTS -server -Xms512m -Xmx1024m -XX:PermSize=256M -XX:MaxPermSize=512m"

12345
```

注意：这里的几个 JVM 参数含义如下：

- `-Xms`: 使用的最小堆内存大小
- `-Xmx`: 使用的最大堆内存大小
- `-XX:PermSize`: 内存的永久保存区域大小
- `-XX:MaxPermSize`: 最大内存的永久保存区域大小

这几个参数也不是配置越大越好，具体要根据所在机器实际内存和使用大小配置。

## 2、修改 Jenkins 主目录

Linux 下 Jenkins 默认安装目录为 `/var/lib/jenkins/`，这个目录磁盘空间有限，长时间使用会导致磁盘空间不够，建议修改为其他大磁盘空间目录。这里修改安装目录有两种方式，一种是配置为系统环境变量中，一种是配置到 Tomcat 容器环境变量中。

配置 `JENKINS_HOME` 到系统环境变量里面

```
# 注意：如果一台机器只安装一个 Jenkins 时，可以配置如下。
$vim /etc/profile
...
export JENKINS_HOME=/data0/jenkins
export PATH=$PATH:$JENKINS_HOME
# 使配置生效
$ source /etc/profile
1234567
```

配置 `JENKINS_HOME` 到该 Jenkins 启动的 Tomcat 容器环境变量中

```
# 注意：如果一台机器上边安装多个 Jenkins 时，不能配置 JENKINS_HOME 到系统环境变量里面，
# 需要配置 JENKINS_HOME 到该 Jenkins 启动的 Tomcat 容器配置里面，这样可以区分不同的 Jenkins 目录。
$ vim /data0/scm/apache-tomcat-7.0.85/conf/context.xml
<Context>
    ...
    # 增加以下配置，优先获取该配置路径。
    <Environment name="JENKINS_HOME" value="/data0/jenkins" type="java.lang.String"/>
</Context>
12345678
```

这里要说明一下，如果一台机器上只安装了一个 Jenkins 服务时，可以配置 `JENKINS_HOME` 到系统环境变量里面，如果安装了多个 Jenkins 服务时，不能这么配置，因为 Jenkins 会读取系统环境变量中 `JENKINS_HOME` 作为主目录安装，那样会存在配置覆盖的问题。此时应该采用第二种方式，各自配置 `JENKINS_HOME` 到自己启动的 Tomcat 容器环境变量中，Jenkins 会优先读取该容器环境变量作为各自的主目录安装。

附 Jenkins 寻找 `JENKINS_HOME` 环境变量的顺序为：首先读取容器环境变量，如果没有，则读取系统环境变量，如果还没有，则使用默认路径安装。

## 3、配置优化减少磁盘空间占用

Jenkins 运行 Job 构建比较多时，如果没有配置好清理策略的话，会导致占用磁盘空间比较大，最终由于磁盘空间不够导致构建失败的问题。

### 3.1、丢弃旧的构建配置

我们可以在 Job 中配置丢弃旧的构建，通过设置 “保持构建的天数” 和 “保持构建的最大个数” 两个参数，控制该 Job 最大保存构建数量。
![这里写图片描述](https://img-blog.csdn.net/20180609212230896?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180609212253318?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如上图所示，我配置了最大保持 3 天之内的构建，如果超过 3 天的构建，则会在Job 执行前被清理掉。同时配置了最大保持构建数量为 10 个，意思就是如果 3 天内构建次数如果超过 10 次，则最多保留最近执行的 10 个构建。这样配置的好处，除了能够自动清理一些 Build 之外，还能够为我们代码执行远程停止 Job Build 时，缩短停止时间，下边会讲到。

### 3.2、修改工作空间和构建记录根目录

Jenkins 工作主要分为安装主目录，工作空间目录以及构建记录目录，默认配置路径如下图所示：
![这里写图片描述](https://img-blog.csdn.net/20180609212451982?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如果我们修改了 Jenkins 安装主目录之后，因为工作空间目录是在安装主目录下的 workspace 目录，构建目录在安装主目录下的 builds 目录，这样运行 Job 执行构建比较多时，还会存在磁盘空间不够的问题，那么此时可以在 “系统管理” —> “系统配置” —> “高级” 分别修改工作空间根目录和构建记录根目录，指向其他磁盘空间即可。

## 4、设置全局属性

适当设置全局属性，可以避免在 Job 中重复写一些固定值，例如输出日志地址、接口请求地址等等，而且当固定值需要修改时，只需要修改一次即可，不用去每个 Job 里面修改，方便维护。我们可以去 “系统管理” —> “系统配置” —> “全局属性” 下增加 Environment variables 键值对，例如如下图：
![这里写图片描述](https://img-blog.csdn.net/20180609212511276?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

那么，在 Job 构建时执行 “ Execute Shell” 使用时，可以直接应用即可，例如如下代码：
![这里写图片描述](https://img-blog.csdn.net/20180609212536162?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 5、JDK/Maven/Gradle 等软件多版本安装

对于一些常用的软件，比如 Jdk、Maven、Gradle等，可能每个项目对软件依赖版本不一样，有的项目依赖 Jdk7，有的依赖 Jdk8，所以为了更好的适配各个项目，可以指定安装多个版本软件，然后 Job 创建时选择其中一个版本使用。这里以 Jdk 为例，去 “系统管理” —> “Global Tool Configuration” —> “JDK” 分别安装 jdk6、Jdk7、Jdk8。
![这里写图片描述](https://img-blog.csdn.net/20180609212557180?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后，在创建 Job 时，选择项目需要的一个版本即可。
![这里写图片描述](https://img-blog.csdn.net/20180609212616132?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 6、设置构建超时时间

有些 Job 在执行构建时，由于某些原因导致构建挂起，耗时比较长，而这些长时间挂起的 Job 会导致 Jenkins 内存占用比较大，性能下降，严重的会直接导致 Jenkins 挂掉。所以，我们需要设置构建超时时间来预防这种事情发生，一旦超过一定的时间，要让 Job 自动停止掉。例如，这里我设置构建超过 30 分钟则将本次 Build 置为失败。
![这里写图片描述](https://img-blog.csdn.net/20180609212638615?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 7、配置视图分类管理 Job

Jenkins 默认视图为 ALL 显示所有 Job 列表，如果 Job 比较多的话，找某个 Job 会不太方便，虽然有 Search 搜索功能，毕竟还是不太方便。这时候，我们可以通过新建视图方式，对 Job 进行分门别类，这样管理和查找起来就方便多啦！例如这里我新建 “List View" 类型视图 “wy”，然后选择该视图所关联的 Job 就可以了。
![这里写图片描述](https://img-blog.csdn.net/20180609212710138?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180609212729718?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这样通过视图切换，查找相关 Job 就方便多了。

## 8、配置多节点管理

一般我们会使用 Jenkins Slave 集群管理来完成日常持续集成操作，使用 Jenkins Slave 一主多从方式，可以将 Job 调度到对应的 Slave 机器上执行，能够大大提高系统并发执行效率。我们可以从 “系统管理” —> “管理节点” —> “新建节点”，设置节点类型为 “Permanent Agent” 名称 “wy_slave0” 的一个从节点，当然有多个节点时，可以创建多个。创建完毕之后，此时插件还属于不可用状态，因为还没有执行关联，具体关联方式可以参照 Jenkins 上节点关联说明（如下图，这里忽略操作），关联完毕之后，我们就可以在新建 Job 中配置指定那个 Slave 节点运行了。
![这里写图片描述](https://img-blog.csdn.net/20180609212806671?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180609212824308?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180609212900130?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这里，有两种方式指定 Job 在那个 Slave 节点运行，一种是对于自由风格类型的 Job，我们可以通过在 “Restrict where this project can be run” 选项下指定 “Label Expression” 标签指定节点标签即可。
![这里写图片描述](https://img-blog.csdn.net/20180609212921186?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

另一种是对于多配置项目类型的 Job，我们可以通过在 “Configuration Matrix” 先配置 “Slave” 选择 Node/Label 勾选指定一个或多个 Slave 执行。
![这里写图片描述](https://img-blog.csdn.net/20180609212934396?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 9、一些实用插件

Jenkins 的基础配置就能够满足我们日常的基本工作，但是为了提高构建效率和方便维护，Jenkins 上提供了很多实用的插件，使用这些插件，我们可以更加轻松、更加简便、更加高效的执行持续集成和发布流程。下边，我就简单介绍几个我使用的插件。

### 9.1、Locale 插件控制 Jenkins 页面国际化

默认 Jenkins 页面显示语言为英文，对于日常使用语言非英文的用户来说，就显得有点操作不便，该插件提供用户配置 Jenkins 页面国际化语言，例如中文、法语、泰文等等。首先我们需要去 “Manage Jenkins” —> “Manage Plugins” —> “Available” 中选择 “Locale” 插件，点击 “Install without restart”，安装完不需要重启。
![jenkins-locale](https://img-blog.csdnimg.cn/20190214164559881.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=,size_16,color_FFFFFF,t_70)
安装完毕后，可以从 “Manage Jenkins” —> “Configure System” —> “Locale” 下 “Default Language” 输入框输入对应的语言英文缩写码，例如这里我想要简体中文，那么就需要填写 `zh_CN`，并勾选 “Ignore browser preference and force this language to all users” 复选框，来强制所有用户使用该配置。
![jenkins-locale](https://img-blog.csdnimg.cn/20190214164625761.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=,size_16,color_FFFFFF,t_70)
点击 “Save”，再次查看页面，页面大部分都已经显示为对应的中文简体了，包括各个 Job 配置页面部分也显示为中文简体了，这下很直观了。
![jenkins-locale](https://img-blog.csdnimg.cn/20190214164652712.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=,size_16,color_FFFFFF,t_70)
![jenkins-locale](https://img-blog.csdnimg.cn/2019021416471042.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=,size_16,color_FFFFFF,t_70)

### 9.2、Build Name Setter & Description Setter 插件支持自定义 Build 名称和描述

Build Name Setter 和 Description Setter 插件可以支持修改每次构建的 Name 名称和 Description 描述信息，默认 Jenkins 每次构建名称为 `#ID + 构建时间`（ID 自增），且无描述信息。这样的弊端就是当某次构建出现了错误或者要排查某次构建信息，除了按构建时间区间挨个去点开日志，别无他法，而该插件可以很好的解决该问题。使用该插件，我们可以为每次构建设置自定义名称和描述信息，名称支持从文件中读取和 `macro` 模板，描述信息还支持 `RegEx` 表达式来从构建日志中提取信息。首先我们需要去 “系统管理” —> “管理插件” —> “可选插件” 中选择 “Build Name Setter” 和 “Description Setter” 插件，安装完不需要重启。
![jenkins-build-setter](https://img-blog.csdnimg.cn/20190214164738912.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=,size_16,color_FFFFFF,t_70)
接下来我们创建一个简单的示例 Job 来使用一下这两个插件。新建名称为 “wy_tt” 的 Job，接收两个参数 `version` 和 `branch`，然后配置 Build Name 为 `# ${BUILD_NUMBER}-${branch}`，Build Description 为 `构建版本：${version}`。
![jenkins-build-setter](https://img-blog.csdnimg.cn/20190214164815590.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=,size_16,color_FFFFFF,t_70)
执行几次构建，每次修改传递的参数，此时去 Job 左侧构建历史里面，就可以看到每次构建的一些重要的辅助信息，这样将大大方便我们排查问题了。当然，我们还可以更高级的使用该插件，比如从文件中读取参数，使用表达式从构建日志中匹配 Key 值，这样功能将更加强大了。
![jenkins-build-setter](https://img-blog.csdnimg.cn/20190214164840869.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=,size_16,color_FFFFFF,t_70)

### 9.3、Managed Script 插件管理脚本文件

该插件是为了在管理文件时创建 Script 脚本文件，然后在 Job 中配置直接使用，方便脚本的统一管理和维护。首先我们需要去 “系统管理” —> “管理插件” —> “可选插件” 中选择 “Managed script” 插件，安装重启即可。
![这里写图片描述](https://img-blog.csdn.net/20180609212959352?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

安装完毕后，可以从 “系统管理” —> “Managed files” —> “Add a new Config” 选择 “Managed script file” 类型，创建一个新的 shell 脚本文件，然后输入我们要执行的脚本代码。这里我创建了两个脚本，分别为 `before-build-step-shell` 和 `after-build-step-shell`，意思很明确了，前者在构建前执行的一些操作，后者在构建后执行的一些操作。
![这里写图片描述](https://img-blog.csdn.net/20180609213014496?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180609213031477?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

注意: 这里的脚本可以使用一些 Jenkins 系统的环境变量参数、参数化构建时传递的参数以及系统命令哦。

创建完毕后，我们在 Job 中构建处选择 “Execute managed script” 就可以使用这些脚本了。
![这里写图片描述](https://img-blog.csdn.net/20180609213059824?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 9.4、PostBuildScript 插件根据 Build 状态执行脚本

推荐安装 PostBuildScript 插件，该插件可以在构建后操作中，根据构建结果状态，执行对应的脚本操作，很实用的一个插件。同上安装该插件，重启 Jenkins 完毕插件生效后，Job 中构建后操作处选择 “Execute Scripts” ，然后在 “Add build step” 中选择 “Execute shell” 等选项（当然也可以配合上一个插件，那么这里就选择 “Execute managed script”），下边选择一个 build 状态条件值，如果选择 SUCCESS 状态，那么该脚本只有在 Build 成功时才会执行，其他状态依次类推，状态可以多选哦，多选代表多种状态都能下触发。
![这里写图片描述](https://img-blog.csdn.net/20180609213125670?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FpeGlhb3lhbmcxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 9.5、Jenkins2.0 Pipeline 插件执行持续集成发布流程

Jenkins 2.0 的精髓是 Pipeline as Code，是帮助 Jenkins 实现 CI 到 CD 转变的重要角色。Pipeline是一套运行于 Jenkins 上的工作流框架，将原本独立运行于单个或者多个节点的任务连接起来，实现单个任务难以完成的复杂发布流程。Pipeline 中任何发布流程都可以表述为一段 Groovy 脚本，并且 Jenkins 支持从代码库直接读取脚本。使用 Pipeline 执行持续集成发布流程好处是显而易见的，它可以把以前需要多个节点上多个 Job 使用一段脚本来替代，而且脚本语言功能强大，可以很好的完成一些复杂的流程操作，推荐大家尝试使用。这里就不多说了，详细可以参考之前文章 [初试Jenkins2.0 Pipeline持续集成](https://blog.csdn.net/aixiaoyang168/article/details/72818804)。

### 9.6、Kubernetes Plugin 插件动态创建 Jenkins Slave

我们知道传统的 Jenkins Slave 一主多从方式会存在一些痛点，比如 Master 单点故障，Slave 配置环境差异，资源分配不均衡等导致可靠性和可维护性比较差，而使用 Kubernetes Plugin 插件可以动态的创建和删除 Jenkins Slave 节点，使用它可以很好的保证服务高可用，动态伸缩合理使用资源，以及良好的扩展性。使用该插件后，它的工作流程大致为：当 Jenkins Master 接受到 Build 请求时，会根据配置的 Label 动态创建一个运行在 Docker Container 中的 Jenkins Slave 并注册到 Master 上，当运行完 Job 后，这个 Slave 会被注销并且 Docker Container 也会自动删除，恢复到最初状态。这里也不多说了，详细可以参考之前文章 [初试 Jenkins 使用 Kubernetes Plugin 完成持续构建与发布](https://blog.csdn.net/aixiaoyang168/article/details/79767649)。

## 10、JAVA 代码触发 Jenkins Job 创建、删除、停止等操作。

Jenkins Job 创建、删除、构建等操作，除了在页面手动操作外，我们还可以通过 Jenkins API 接口执行对应操作，详细接口可参考 Jenkins
REST API 文档地址：`http://<jenkins_url>/api`。这里我要演示的是使用 Jenkins-client.jar 包，使用 JAVA 代码操作如何创建、删除、停止、触发构建等，使用代码触发 jenkins 相关操作，好处就是自己可控，这样可以配合自己的业务需要，随时启动或者新建 Job 啦。

```
public class JenkinsUtils {
	
	private static String jenkins_url = "http://127.0.0.1/jenkins/";
	private static String jenkins_user = "admin";
	private static String jenkins_token = "1b356d175432ed0d34c440d68d00fe49";
	
	/**
	 * 通过模板创建 Job
	 * @param jobName
	 * @param jobTemplate
	 * @return
	 */
	public static boolean createJobFromTemplate(String jobName, String jobTemplate) {
	    try {
	        URI uri = new URI(jenkins_url);            
	        JenkinsServer jenkins = new JenkinsServer(uri, jenkins_user, jenkins_token);
	        String template = jenkins.getJobXml(jobTemplate);
	        Document doc = DocumentHelper.parseText(template);
	        String newConfigXml = doc.asXML();
	        jenkins.createJob(jobName, newConfigXml, false);
	    }catch (Exception e){
	        e.printStackTrace();
	        return false;
	    }
	    return true;
	}
	
	/**
	 * 删除/禁用 Job
	 * @param jobName
	 * @return
	 */
	public static boolean deleteJob(String jobName){
		try {
			URI uri = new URI(jenkins_url);            
            JenkinsServer jenkins = new JenkinsServer(uri, jenkins_user, jenkins_token);
            jenkins.deleteJob(jobName, false);
            jenkins.disableJob(jobName, false);
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
		return true;
	}
	
	/**
	 * 启动 Job 构建
	 * @param jobName
	 * @param params
	 * @return
	 */
	public static boolean startJob(String jobName, String params){
		try {
			String buildUrl = jenkins_url + jobName + "/buildWithParameters?" + params;
			HttpUtils.HttpGet(buildUrl);
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
		return true;
	}
	
	/**
	 * 停止正在构建中的 Job，先清除等待队列中的 build，在停止运行中的 build
	 * @param jobName
	 * @return
	 */
	public static boolean stopJob(String jobName){
		try {
			URI uri = new URI(jenkins_url);
            JenkinsServer jenkins = new JenkinsServer(uri, jenkins_user, jenkins_token);
            // 先 kill 掉 queue 里面的 build
            QueueItem qi = jenkins.getJob(jobName).getQueueItem();
            if(qi != null){
				HttpUtils.HttpPost(jenkinsUrl + "queue/cancelItem?id=" + qi.getId());
            }
            // 在 kill 掉正在运行中的 build
            List<Build> bulidsList = jenkins.getJob(jobName).getAllBuilds();
            for(Build b:bulidsList){
            	if(b.details().isBuilding()){
            		try{
            			b.Stop();
            		}catch(Exception ee){
            			ee.printStackTrace();
            			return false;
            		}
            	}
            }
		} catch(Exception e) {
			e.printStackTrace();
			return false;
		}
		return true;
	}
}
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990919293949596
```

这里有一个地方要注意，在停止构建中的 Job 时，这里是遍历所有 Build，然后在 Kill 掉运行中的 Build，如果 Build 历史比较多的时候，会耗时比较久，这将会导致立马重新执行该 Job Build 时， Build 会被异常 Abort 掉。。。 也尝试过获取最后一次 Build 执行 Stop 操作，好像也不太好使。所以这里大家可以通过上边 `3.1、丢弃旧的构建配置` 中的操作，减少构建历史记录，这样就可以很快执行完毕，就不会出现上述问题了。