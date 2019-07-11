## [Jmeter基础之---jmeter基础概念](https://www.cnblogs.com/fnng/archive/2012/12/21/2828440.html)



**JMeter** **介绍**： 一个非常优秀的开源的性能测试工具。

 

**优点**：你用着用着就会发现它的重多优点，当然不足点也会呈现出来。

 

**从性能工具的原理划分**：

Jmeter工具和其他性能工具在原理上完全一致，工具包含4个部分：

（1）负载发生器：用于产生负载，通常以多线程或是多进程的方式模拟用户行为。

（2）用户运行器：通常是一个脚本运行引擎，用户运行器附加在线程或进程上，根据脚本要求模拟指定的用户行为。

（3）资源生成器：用于生成测试过程中服务器、负载机的资源数据。

（4）报表生成器：根据测试中霍地的数据生成报表，提供可视化的数据显示方式。

 

**测试计划元件**

------

  

**Test Plan (测试计划)**：用来描述一个性能测试，包含与本次性能测试所有相关的功能。也就说本的性能测试的所有内容是于基于一个计划的。

下面看一下一个计划下面都有哪些主要的功能模块（右键单击“测试计划”弹出菜单）。

![img](https://pic002.cnblogs.com/images/2012/311516/2012122118565220.jpg)

 

 

**Threads （Users）线程 用户**

![img](https://pic002.cnblogs.com/images/2012/311516/2012122118593189.png)

虽然有三个添加线程组的选项，名字不一样， 创建之后，其界面是完全一样的。之前的版本只有一个线程组的名字。现在多一个setUp theread Group 与terDown Thread Group 

1) setup thread group  

　　一种特殊类型的ThreadGroup的，可用于执行预测试操作。这些线程的行为完全像一个正常的线程组元件。不同的是，这些类型的线程执行测试前进行定期线程组的执行。

2) teardown thread group.  

　　一种特殊类型的ThreadGroup的，可用于执行测试后动作。这些线程的行为完全像一个正常的线程组元件。不同的是，这些类型的线程执行测试结束后执行定期的线程组。

　　可能你还是不太理他们与普通的线程组有什么不同。 如果您用过junit，想必你不会对setup ，teardown这2个字眼陌生。 即时每用过，也没关系。 熟悉loadrunner的应该知道，loadrunner的脚本除了action里是真正的脚本核心内容，还有初始化“环境”的初始化脚本和测试完毕后对应的清除信息的脚本块。 那么这里 setup thread group 和 teardown thread group 就是分别指这两部分。  其实从本质上来看，他们并没有什么不同。

3) thread group(线程组).

​     这个就是我们通常添加运行的线程。通俗的讲一个线程组,，可以看做一个虚拟用户组，线程组中的每个线程都可以理解为一个虚拟用户。线程组中包含的线程数量在测试执行过程中是不会发生改变的。

 

 

**测试片段（Test Fragment）**

 ![img](https://pic002.cnblogs.com/images/2012/311516/2012122119005219.png)

　　测试片段是在2.5版本之后新加的一个选项。

　　测试片段元素是控制器上的一个种特殊的线程组，它在测试树上与线程组处于一个层级。它与线程组有所不同，因为它不被执行，除非它是一个模块控制器或者是被控制器所引用时才会被执行。

 

 

控制器

JMeter有两种类型的控制器：取样器（sample）和逻辑控制器（Logic Controller），用这些原件来驱动处理一个测试。

 

**取样器（Sampler）**

 ![img](https://pic002.cnblogs.com/images/2012/311516/2012122119021232.png)

  　　取样器（Sample）是性能测试中向服务器发送请求，记录响应信息，记录响应时间的最小单元，JMeter 原生支持多种不同的sampler ，如 HTTP Request Sampler 、 FTP  Request Sample 、TCP  Request Sample 、JDBC Request Sampler 等，每一种不同类型的 sampler 可以根据设置的参数向服务器发出不同类型的请求。（在jmeter 的所有sampler 中，Java Request Sampler 和 Beanshell Request Sampler 是两种特殊的可定制的 Sampler ，后面会深入讨论。）

 

 

 

**逻辑控制器（Logic Controller）**

![img](https://pic002.cnblogs.com/images/2012/311516/2012122119024323.png)

　　逻辑控制器，包括两类无件，一类是用于控制test plan 中 sampler 节点发送请求的逻辑顺序的控制器，常用的有 如果（If）控制器 、switch Controller 、Runtime Controller、循环控制器等。另一类是用来组织可控制 sampler 来节点的，如 事务控制器、吞吐量控制器。

 

  

 

**配置元件（Config Element）**

 ![img](https://pic002.cnblogs.com/images/2012/311516/2012122119030867.png)

　　配置元件（config element）用于提供对静态数据配置的支持。CSV Data Set config 可以将本地数据文件形成数据池（Data Pool），而对应于HTTP Request Sampler和 TCP Request Sampler等类型的配制无件则可以修改Sampler的默认数据。（例如，HTTP Cookie Manager 可以用于对 HTTP Request Sampler 的cookie 进行管理）

 

 

**定时器（Timer）**

![img](https://pic002.cnblogs.com/images/2012/311516/2012122119032792.png)

 　　定时器（Timer）用于操作之间设置等待时间，等待时间是性能测试中常用的控制客户端QPS的手端。类似于LoadRunner里面的“思考时间”。JMeter 定义了Bean Shell Timer、Constant Throughput Timer、固定定时器等不同类型的Timer。

 

 

**前置处理器（Per Processors）**

![img](https://pic002.cnblogs.com/images/2012/311516/2012122119034851.png)

　　用于在实际的请求发出之前对即将发出的请求进行特殊处理。例如，HTTP URL重写修复符则可以实现URL重写，当RUL中有sessionID 一类的session信息时，可以通过该处理器填充发出请求的实际的sessionID 。

 

 

**后置处理器（Post Processors）**

![img](https://pic002.cnblogs.com/images/2012/311516/2012122119041073.png)

　　用于对Sampler 发出请求后得到的服务器响应进行处理。一般用来提取响应中的特定数据（类似LoadRunner测试工具中的关联概念）。例如，XPath  Extractor 则可以用于提取响应数据中通过给定XPath 值获得的数据。

 

 

**断言（Assertions）**

 ![img](https://pic002.cnblogs.com/images/2012/311516/2012122119043024.png)

　　 断言用于检查测试中得到的相应数据等是否符合预期，断言一般用来设置检查点，用以保证性能测试过程中的数据交互是否与预期一致。

  

 

**监听器（Listener）**

 ![img](https://pic002.cnblogs.com/images/2012/311516/2012122119045478.png)

 　　这个监听器可不是用来监听系统资源的元件。它是用来对测试结果数据进行处理和可视化展示的一系列元件。 图行结果、查看结果树、聚合报告。都是我们经常用到的元件。

 

到此，我们已经简单了解了jmeter的基本组成原件，我们后序的性能测试工作也就是使用这些元件来完成测试任务。



---------------------

# [JMETER的三个线程组](https://www.cnblogs.com/ldmjy/p/9059182.html)

 

Jmeter有三个线程组分类：Thread Group、setUp Thread Group、tearDown Thread Group,如下图所示：

![img](https://images2018.cnblogs.com/blog/1403726/201805/1403726-20180519081914669-2033887222.png)

（1）、thread group(线程组)

　　这个就是我们通常添加运行的线程。通俗的讲一个线程组，可以看做一个虚拟用户组，线程组中的每个线程都可以理解为一个虚拟用户。

（2）、setup thread group

　　一种特殊类型的ThreadGroup的，可用于执行预测试操作。这些线程的行为完全像一个正常的线程组元件。不同的是，这些类型的线程执行测试前进行定期线程组的执行；类似LoadRunner的init，测试开始时进行初始化的工作。

（3）、teardown thread group

　　一种特殊类型的ThreadGroup的，可用于执行测试后动作。这些线程的行为完全像一个正常的线程组元件。不同的是，这些类型的线程执行测试结束后执行定期的线程组；类似LoadRunnner的end，测试结束时进行回收工作。

# 线程组参数设置的意义

![img](https://images2018.cnblogs.com/blog/1403726/201805/1403726-20180519143543751-1200389637.png)

我们把线程组的设置分成3个区域：

区域一：在采样器失败后怎么处理（LoadRunner里面也有类似的运行设置选项，对比去学习）：

　　1、continue：继续执行接下来的操作；

　　2、Start Next Thread Loop：开始下一次循环；

　　3、stop Thread：停止线程，退出该线程（不再执行此线程的操作）；

　　4、stop Test：等待当前执行的采样器结束后，结束整个测试；

　　5、Stop Test Now：马上停止测试；

区域二：线程属性

　　1、Number of Threads(users)：线程数，相当于模拟的用户数量；

　　2、Ramp-up Period(in seconds)：达到指定线程需要的时间，例如线程数为100，时间设定为10s，那么就是10s加载　　　　100个线程，每秒启动的线程数=100/10=10；

　　3、Loop Count：如果填具体的数值，就是循环对应的次数；如果选择“Forever”，则一直执行下去，直到手动停止；

　　4、Delay Thread creation until needed：延迟线程创建，直到需要才创建。

区域三：调度器配置

　　需要选中调度器（scheduler），调度器配置才生效。

![img](https://images2018.cnblogs.com/blog/1403726/201805/1403726-20180519151531063-988047449.png)

 LoadRunner中controller场景设置处也有类似设置，一般用于定时去跑场景，如我们需要晚上某个时间段去执行场景，我们可以设置场景执行的时间，下班前启动Controller，此时不会真正去执行，需要到达指定时间才去执行。



----------------

# [Jmeter 在什么情况下定义多个thread group？](https://www.cnblogs.com/autotest/p/3831008.html)



Jmeter里面有三种线程组：setUp thread group, TearDown thread group, thread group. 如果想定义100个用户登录系统，60个用户做A操作，40用户做B操作。我认为可以这样定义：
\1. 测试计划中复选框“独立运行每个线程组”应该是非选中状态；
\2. 在测试计划中添加一个SetUp thread group, 设置用户数是100；
\3. 还要添加两个普通线程组，分别定义60线程和40线程。

 

经过一段时间的摸索，我发现上面的描述是错误的。

如果新定义线程组，该线程组与前面的setup thread group线程组不一定会联系在一起的。

 

设定一个场景：

定义100个人登录系统，60个人做A操作30次，40个人做B操作20次，如何实现？

请求数：60 * 30 + 40 * 20 = 2600

每个人请求数：2600 / 100 = 26

在Jmeter里面，设定线程数：100

定义26次循环。

登录请求放在一次性控制器里面

A，B请求放在吞吐量控制器里面，其中A：B请求的百分比是：70：30



------------------------

# Jmeter 线程组

2013年11月26日 23:36:48 [狂沙沙陌](https://me.csdn.net/lxgails00) 阅读数 2845



对于jmeter而言，所有的测试脚本均是以测试计划的形式进行的，而对于每一个测试计划也是由各种元件组合在一起，各元件在脚本计划中均有不同的作用

适当使用元件组合达到测试目的，以下是对各元件的简要介绍：

一. 线程组件

​    \1. 线程组件是模拟多并发行为必须依靠的组件，也是每一个测试脚本的起始点，所有的controllers和samplers都必须基于线程组件才是有意义的，其他的组件比如listeners可以直接放在测试计划的节点下与线程组件并列，在这种情况下，此listeners监听的就是整个线程组。线程组就是控制线程数量的。

​    \2.  线程组设置

![ddd](https://img-blog.csdn.net/20131126233613687?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHhnYWlsczAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



​        2.1 名称和注释都是为了使测试计划更易懂，所以不做解释，你想怎么填都行

​        2.2 在取样器（sampler）错误后要执行的动作

​            

| 继续            | 遇到sampler执行出错时继续进行完成所有sampler                 |
| --------------- | ------------------------------------------------------------ |
| start next loop | 遇到sampler执行出错时，直接进行下次循环，当前循环剩下的所有sampler不再执行 |
| 停止线程        | 遇到sampler执行出错时，当前线程停止进行，其他线程继续        |
| 停止测试        | 遇到sampler执行出错时，当前所有线程执行完当前循环停止进行    |
| stop Test now   | 遇到sampler执行出错时，当前所有线程立即停止                  |

​        2.3 线程属性

​             线程数：设置并发用户线程数量，即通常意义的并发用户数

​            Ramp-Up Period （in seconds）：设置并发用户加载时间，假设并发用户数设置为100，此处设置为10，则认为jmeter会在10s内加载完成100并发用户数，也就是说每隔10秒加载一个

​            循环次数：输入框与复选框只能选择一种，不能同时被选择为场景循环方式；输入框输入数字则被认为是当前线程组所控制的脚本区域的执行次数；【永远】复选框选择后，输入框内的数字将失效，启动后脚本将一直执行下去。

​            调度器：对于调度而言，顾名思义就是我们想在什么时间开始运行，运行多长时间自动停止诸如此类的场景需求均可以在此设置。

- 启动时间：在设置时间点开始运行场景，比如你想在下班后开始运行，如果时间小于当前时间，启动后立即执行，大于当前时间，启动后等待本地时间到了自动运行
- 结束时间：在设置时间点结束运行场景，比如上班前结束运行，结束时间如果小于开始时间或者当前时间，自动运行后立即终止
- 持续时间：设置当前场景持续运行的时间
- 启动延迟：设置场景启动后多长时间再开始真正运行，比如你想在下班后5分钟开始运行场景

​        其中持续时间和启动延迟的优先级高于启动时间和结束时间，也就是说当你设置了持续时间或者启动延迟，那么你的启动时间或者结束时间将失效，

​      几种设置场景组合：

- ​    不勾选【永远】，选择【调度器】，设置完成后，启动运行也只会运行当前并发用户数*循环次数，运行完成即会结束。所以此中选择可用于希望未来某时间点开始运行完成并发用户数*循环次数的场景
- 勾选【永远】，设置启动时间及结束时间，持续时间和启动延迟均为空，可用于希望未来某时间点自动运行并且到未来某时间点自动结束的场景
- 勾选【永远】， 设置 持续时间和启动延迟， 可用于希望在启动多久后自动运行并持续多长时间的场景

二、setUp Thread Group 和tearDown Thread Group
     对于setup Thread Group和tearDown Thread Group来说，从字面意思上来看就是安装线程组和卸载线程组，所以可以理解为对于线程组的初始化和完成时处理，setup Thread Group是所有我们真正开始线程并发之前的准备工作，必须是在线程组开始之前完成的并且拥有自己独立的线程设置；
[url=]tearDown Thread Group正好和前者相反，是在所有线程组运行结束后进行的，如果线程组场景一直在执行中将永远也轮不到[/url]tearDown Thread Group；这两种的运行场景可以用在登陆和退出并非我们的压力目的时，仅执行一次或者与主场景执行并发不一致的时候将登陆放在setup Thread Group，将退出放在tearDown Thread Group。

![ss](https://img-blog.csdn.net/20131127231157921?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHhnYWlsczAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

上图中开始线程组为setup Thread Group，结束线程组为tearDown Thread Group，进行中线程组和线程组1都是普通线程组（Thread Group），添加一个聚合报告，做实验时聚合报告请勾选【include group name in label】，勾选后运行结果会再聚合报告中显示出线程名字。
分别在“开始”中录制登陆脚本，在“进行中线程”和“线程组1”录制登陆后的操作脚本，在“结束”中录制退出系统的脚本，设置各自并发后启动启动后立即查看聚合报告中生成的记录，会发现先完成“开始”线程组的各种请求，再完成普通线程组的各种请求（即便有线程组的位置节点在“结束”之后），最后完成“结束”线程组的请求。

    这两种线程组的各种设置同于普通线程组。

---------------------
