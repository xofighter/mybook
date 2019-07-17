# Jmeter简单介绍与搭配Jenkins实现自动化测试实践 

2017-09-11 11:55

**Bioneck 岂安科技研发工程师**

负责 Stalker 项目的部分研发与测试工作，编程爱好者，乐于使用代码解决生活中的重复工作。

最近有个需要依靠 Jmeter+Jenkins 实现自动化测试后部署的需求，于是看了看Jmeter的资料，这里给大家分享下。

**Jmeter简介**

Apache JMeter 是 Apache 组织开发的基于 Java 的压力测试工具。用于对软件做压力测试，它最初被设计用于 Web应用测试，但后来扩展到其他测试领域。 它可以用于**测试静态和动态资源**，例如静态文件、Java 小服务程序、CGI 脚本、Java 对象、数据库、FTP 服务器， 等等。JMeter 可以用于对服务器、网络或对象模拟巨大的负载，来自不同压力类别下测试它们的强度和分析整体性能。另外，JMeter 能够对应用程序做功能/回归测试，通过创建带有断言的脚本来验证你的程序返回了你期望的结果。为了最大限度的灵活性， JMeter 允许使用正则表达式创建断言。

Apache jmeter 可以用于对静态的和动态的资源（文件，Servlet，Perl 脚本，java 对象，数据库和查询，FTP 服务器等等）的性能进行测试。它可以用于对服务器、网络或对象模拟繁重的负载来测试它们的强度或分析不同压力类型下的整体性能。你可以使用它做性能的图形分析或在大并发负载测试你的服务器/脚本/对象。

**>>>>**

**meter安装**

Jmerter 是基于 java的，所以安装 Jmeter 之前必须先安装 JDK 依赖，JDK 安装的方法网上许多这里就不多介绍了（建议安装8.0以上版本）。

安装好 java 后到 Jmeter 官网下载对应系统的压缩包解压到本地任意位置就可以使用了，这里我建议下载3.0以上版本的 Jmeter，因为从3.0开始 Jmeter 可以直接生成可视化的 HTML 报告，不再需要安装插件来生成。

**>>>>**

**Jmeter测试组件**

（1）负载发生器：用于产生负载，通常以多线程或是多进程的方式模拟用户行为。

（2）用户运行器：通常是一个脚本运行引擎，用户运行器附加在线程或进程上，根据脚本要求模拟指定的用户行为。

（3）资源生成器：用于生成测试过程中服务器、负载机的资源数据。

（4）报表生成器：根据测试产生的数据生成报表，提供可视化的数据显示方式。

**Test Plan (测试计划)：**用来描述一个性能测试，包含与本次性能测试所有相关的功能。也就说本次的性能测试的所有内容是于基于一个计划的。

下面看一下一个计划下面都有哪些主要的功能模块（右键单击“测试计划”弹出菜单）。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/033442c9251c4a95b8a018428df7ef70.jpg)

**Threads （Users）线程 用户**

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/a9dc12fc531240be9eafc7b8b15256a8.jpg)

虽然有三个添加线程组的选项，名字不一样， 创建之后，其界面是完全一样的。之前的版本只有一个线程组的名字。现在多一个 setUp theread Group 与 terDown Thread Group。

setup thread group

可用于执行预测试操作，这种类型的线程执行测试前进行定期线程组的执行。

teardown thread group.

可用于执行测试后动作，这种类型的线程执行测试结束后执行定期的线程组。

thread group(线程组).

这个就是我们通常添加运行的线程。通俗的讲一个线程组,，可以看做一个虚拟用户组，线程组中的每个线程都可以理解为一个虚拟用户。线程组中包含的线程数量在测试执行过程中是不会发生改变的。

**>>>>**

**测试片段**

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/faa22a07ac1847b69d9653e50b9e295b.jpg)

**测试片段**是在2.5版本之后新加的一个选项。

**测试片段元素**是控制器上的一个种特殊的线程组，它在测试树上与线程组处于一个层级。它与线程组有所不同，因为它不被执行，除非它是一个模块控制器或者是被控制器所引用时才会被执行。

**组成**

**>>>>**

**控制器**

JMeter 有两种类型的控制器：取样器（sample）和逻辑控制器（Logic Controller），用这些原件来驱动处理一个测试。

**>>>>**

**取样器（Sample）**

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/18100c8c6c12427e84738f396e30ba4a.webp)

取样器（Sample）是性能测试中向服务器发送请求，记录响应信息，记录响应时间的最小单元，JMeter 原生支持多种不同的 sampler ，如 HTTP Request Sampler 、 FTP Request Sample 、TCP Request Sample 、JDBC Request Sampler 等，每一种不同类型的 sampler 可以根据设置的参数向服务器发出不同类型的请求。

**>>>>**

**逻辑控制器（Logic Controller）**

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/c99d333c995f4e05934179fcdb6eb003.webp)

逻辑控制器，包括两类元件，一类是用于控制 test plan 中 sampler 节点发送请求的逻辑顺序的控制器，常用的有 如果（If）控制器 、switch Controller 、Runtime Controller、循环控制器等。另一类是用来组织可控制 sampler 来节点的，如 事务控制器、吞吐量控制器。

**>>>>**

**配置元件（Config Element）**

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/be86d143a81247f996ce7dc763b3c2be.jpg)

配置元件（config element）用于提供对静态数据配置的支持。CSV Data Set config 可以将本地数据文件形成数据池（Data Pool），而对应于 HTTP Request Sampler 和 TCP Request Sampler 等类型的配置原件则可以修改 Sampler 的默认数据。（例如，HTTP Cookie Manager 可以用于对 HTTP Request Sampler 的cookie 进行管理）

**>>>>**

**定时器（Timer）**

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/97ae35b7dbec428fab028f2b3c4f6fde.jpg)

定时器（Timer）用于操作之间设置等待时间，等待时间是性能测试中常用的控制客户端 QPS 的手段。类似于 python 中的 sleep。JMeter 定义了 Bean Shell Timer、Constant Throughput Timer、固定定时器等不同类型的 Timer。

**>>>>**

**前置处理器（Per Processors）**

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/4b83b12077444ed896756d434334919d.jpg)

用于在实际的请求发出之前对即将发出的请求进行特殊处理。例如，HTTP URL 重写修饰符则可以实现URL重写，当 RUL中有 sessionID 一类的 session 信息时，可以通过该处理器填充发出请求的实际的 sessionID 。

**>>>>**

**后置处理器（Post Processors)**

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/51a709b387a1451099f8037daa8357a4.jpg)

用于对 Sampler 发出请求后得到的服务器响应进行处理。一般用来提取响应中的特定数据。例如，正则表达式提取器 则可以用于提取响应数据中通过匹配正则表达式获得的数据。

**>>>>**

**断言（Assertions）**

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/016388066cfc47779de7bd1b42e7d146.jpg)

断言用于检查测试中得到的相应数据等是否符合预期，断言一般用来设置检查点，用以保证性能测试过程中的数据交互是否与预期一致。

**>>>>**

**监听器（Listener）**

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/d2a6e045933f4b29be9006ae43980868.jpg)

监听器可以用来对测试结果数据进行处理和可视化展示的一系列元件。 图行结果、查看结果树、聚合报告。都是我们经常用到的元件。

以上就是 Jmeter 的基本组成元件，我们用到的绝大部分测试计划就是依靠以上元件完成。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/0abe9a72692e408f84ca5a77a9cc9636.gif)

接下来我就根据我们的项目测试实例来实际演示一下以上元件的用法，目前项目的测试需求主要是业务测试，如果需要压力测试只需要把检查数据正确性的步骤去掉，简单的测试流程为向一个接口 get 请求将数据传输过去后，检查日志和数据库中是否有正确的数据。

这是我配置好后的测试计划：

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/43574f33d1e049bdacbf96081ee26dfe.jpg)

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/0abe9a72692e408f84ca5a77a9cc9636.gif)

**添加线程组**

一个测试请求是基于一个线程组完成的。一个测试计划必须有一个线程组。测试计划添加线程组非常简单。在测试计划右键弹出下拉菜单（添加-->Threads(Users)--->线程组）中选择线程组即可。

jmeter 中 每个测试计划至少需要包含一个线程组，当然也可以在一个计划中创建多个线程组，多个线程组在测试计划下面是并行执行的，也就是说这些线程组是同时被初始化并同时执行线程组下的 Sampler。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/58ebf4eff0414d56b26287f55c299a48.jpg)

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/1429ec9f6bc34e47a5e2f4d037eaf67b.jpg)

线程组主要包含三个参数：**线程数、准备时长（Ramp-Up Period(in seconds)）、循环次数。**

- **线程数**：虚拟用户数。一个虚拟用户占用一个进程或线程。设置多少虚拟用户数在这里也就是设置多少个线程数。
- **准备时长**： 设置的虚拟用户数需要多长时间全部启动。如果线程数为20 ，准备时长为10 ，那么需要10秒钟启动20个线程。也就是每秒钟启动2个线程。
- **循环次数**：每个线程发送请求的次数。如果线程数为20 ，循环次数为100 ，那么每个线程发送100次请求。总请求数为20*100=2000 。如果勾选了“永远”，那么所有线程会一直发送请求，一到选择停止运行脚本。

在这里，因为是检查数据正确性只需要运行一次，所以全部设置为1。

**添加参数**

添加参数常用读取 CSV 文件将文件内容设置为变量，我比较喜欢的是添加Beanshell元件，编写java脚本直接生成数据。

在线程组上右键菜单选择添加--->配置元件--->CSV Data Set Config 就能添加 CSV文 件读取。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/992acfe5965f454ca8c34b2c1e9688fe.jpg)

**主要设置的参数有：**

- FileName: 填写被读取的 CSV 文件地址和文件名。
- File Encoding: 默认为 ANSI。
- Varible Names: 定义文本文件中的参数名，以逗号分隔例如 a,b,c，定义后可在脚本在以 Shell 变量的同样的方式引用。
- Delimiter: 定义分隔符，用于将变量分开，这里定义的某个分隔符，则在 Variable Names 用这里定义的分隔符，例如 a,b,c 那么分隔符就是“,”。
- Recycle on EOF: 设置为 True 后,允许循环取值。
- Stop Thread on EOF: 当 Recycle on EOF 为 false 并且 Stop Thread on EOF为true,则读完 csv 文件中的记录后,停止运行。
- Sharing Mode: 设置是否线程共享。

添加 Beanshell 需要在线程组上右键菜单选择添加--->Sampler--->Beanshell Sampler 就能添加 Beanshell 脚本文件读取。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/0b5b0465dc2f4942822839312d2d158d.jpg)

beanshell 十分强大，除了生成参数外也可以 import java 的包来完成许多复杂的逻辑，这里就不细说了。

参数设置后可以在后续的元件中以 $ {参数名}的形式调用。

**添加HTTP请求**

生成参数后在线程组上右键菜单（添加--->Sampler--->HTTP请求）选择 HTTP 请求。对于 jmeter 来说，取样器（Sampler）是与服务器进行交互的单元。一个取样器通常进行三部分的工作：

1.向服务器发送请求 2.记录服务器的响应数据 3.记录相应时间信息

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/8bab61680f5d4563a0a38fd3d31fa4fd.jpg)

HTTP Sampler 中可以设置的参数很多，这里就说一下一些**经常使用**的：

- 名称：本属性用于标识一个取样器，建议使用一个有意义的名称。

- 注释：对于测试没有任何作用，仅用户记录用户可读的注释信息。

- 协议：向目标服务器发送 HTTP 请求时的协议，可以是 http 或者是 https ，默认值为 http 。

- 服务器名称或 IP ：HTTP 请求发送的目标服务器名称或IP地址。

- 端口号：目标服务器的端口号，默认值为80 。

- 方法：发送HTTP请求的方法，可用方法包括 GET、POST、HEAD、PUT、OPTIONS、TRACE、DELETE 等。

- 路径：目标 URL 路径（不包括服务器地址和端口）。

  Content encoding ：内容的编码方式，默认值为 iso8859。

- 自动重定向：如果选中该选项，当发送 HTTP 请求后得到的响应是302/301时，JMeter 自动重定向到新的页面。

- 同请求一起发送参数 ： 在请求中发送 URL 参数，对于带参数的 URL ，jmeter 提供了一个简单的对参数化的方法。用户可以将 URL 中所有参数设置在本表中（如图中 ${did}就是调用之前生成的参数），表中的每一行是一个参数值对（对应 RUL 中的 key1=value1）。

在这里添加好参数后就可以进入下一步，检查数据。

**添加数据库连接与数据库操作**

要连接数据库首先需要下载不同数据库对应的 JDBC 驱动包，这里我使用的是 mysql，所以下载入 mysql-connector-java.jar，下载后将 jar 包放到Jmeter所在目录的 lib 文件夹中就可以了。

然后选择配置元件中的 JDBC Connection Configuration 添加数据库连接配置。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/14230cc803144a7e880a579384aafd72.jpg)

大部分配置读使用默认就可以，主要配置连接数据库部分：

- Variable Name： 变量名称。需要唯一标识。与 JDBC 取样器中的相对应，决定 JDBC 取样的配置。简单理解就是在 JDBC request 的时候确定去哪个绑定的配置。

- MaxNumber of Connection： 数据库最大链接数

- PoolTimeout： 数据库链接超时，单位 ms

- Idle Cleanup Interval （ms）： 数据库空闲清理的间隔时间，单位 ms

- Transaction Isolation：

  事务间隔级别设置，主要有如下几个选项：（对 JMX加解密） TRANSACTION_REPEATABLE_READ 事务重复读、TRANSACTION_READ_COMMITTED 事务已提交读 、TRANSACTION_SERIALIZABLE 事务序列化 、TRANSACTION_READ_UNCOMMITTED 事务未提交读、TRANSACTION_NODE 事务节点 、DEFAULT 默认、编辑

- Keep-Alive： 是否保持连接

- Max Connection age （ms）：最大连接时长，超过时长的会被拒绝

- Validation Query：验证查询，检验连接是否有效（数据库重启后之前的连接都失效，需要验证查询）

- Database URL：如 jdbc:mysql://localhost:3306/test 表示本地数据库，3306 端口，数据库名称为 test

- JDBCDriver Class： JDBC 的类，如 com.mysql.jdbc.Driver ，不同数据库使用把不同累，可以参考 Jmeter 的文档。

配置好后就可以配置具体的数据库操作。

选择 Sampler 添加 JDBC Request。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/cf77115fced04d2ea2b005b250bf6c6d.webp)

- Variable Name：数据库连接元件名称，填写对应 JDBC Name，
- Query Type：选择对应的数据库操作，路 select，update 等。
- Query：具体的数据库查询语句，可以直接将参数写道 SQL 语句中,也可以使用占位符:?,将参数写到下方的 Parameter velaues 中,
- Parameter Types: 参数类型
- variable names: 返回结果的参数名,使用方法与读取 csv 文件获得的参数相同

获取到数据库查询结果后就可以检查数据正确性了

**数据检查**

需要检查的是 JDBC request 回来的数据，所以断言元件需要在数据库操作的内层。断言的种类有许多，这里就选择最常用的响应断言

右键 JDBC Request ,选择添加---->断言--->响应断言。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/d11f1ad97bdc4a45a9055c016316f87e.webp)

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/fd232016f3714a318abc379ca959ea73.jpg)

**断言的设置是比较简单的：**

- apply to：是应用范围，设定匹配的范围
- Main sample and sub-samples: 匹配范围为当前父取样器，及子取样器
- Main sample only ：仅当前父取样器
- Sub samples only: 仅子取样器
- JMeter Variable：变量值进行匹配，这里需要检查的是 JDBC Request 返回的值所附的变量，所以选择这一项，**注意**：变量名_x的形式意思是取数据返回结果的第x行。

**要测试的响应文字：针对响应数据不同部分进行匹配**

(1) 响应文本：响应服务器返回的文本内容，http 协议排除 header 部分

(2) 响应代码：匹配响应代码，比如 http 请求中‘200’代表成功 (3) 响应信息：匹配响应信息，处理成功返回‘成功’或者“ok”字样 (4) Response Header: 匹配响应头中的信息

**匹配规则：**

- 包括：响应内容包括需要匹配的内容就算成功
- 匹配：响应内容要完全匹配匹配内容，不区分大小写
- equals：完全相等，区分大小写
- substring：响应内容包括匹配内容即为成功。

可以通过添加**断言结果**来查看断言的执行情况。

如果接口响应数据可以与断言匹配上，则测试用例通过，否则不通过。

查看通过结果就需要添加监听器了(Listener)

**添加监听器**

Jmeter 有许多种类型的监听器，比如查看图形结果、聚合报告等，因为这个测试是业务测试，所以这里就选择查看结果树，针对每个元件的运行结果进行查看。

选择监听器--->响应断言 执行测试用例就可以看到测试结果了。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/2cbd0db4e22049c4a01dacf5a9d05c36.webp)

查看结果树，通过的测试通常为绿色。红色则代表失败了。可以查看到取样器结果，请求，响应数据，或是把测试结果写入到文件中。

测试计划配置完成之后,可以选择左上角的文件--->保存测试计划为...，来将测试计划保存到想要的地方。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/c7f31593ff4f44afa86b200e44223340.gif)

以上就是 Jmeter 的大概使用方式与介绍。

我们经常需要 Jenkins 实现一键部署，但是有时候我们可能会因为失误导致部署时的代码不一定是可用的，这时候就可以使用 Jmeter 添加到 Jenkins 中，保证部署时的代码是通过了测试用例的。

接下来介绍 Jmeter 与 Jenkins 搭配使用实现自动化测试部署。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/c7f31593ff4f44afa86b200e44223340.gif)

**使用**

在添加 Jmeter 测试任务之前，Jenkins 需要首先安装两个插件，以方便我们对 Jmeter 的测试结果进行处理：

- **HTML Publisher plugin：**可以让我们方便的在 Jenkins 中直接打开 Jmeter 的结果进行查看。
- **Performance Plugin：**可以将测试结果的 log 以图表的形式整合到 Jenkins 中，还能根据测试结果的错误率与波动率修改 Jenkins 的 job 状态。

安装好插件以后就可以添加测试任务了，选择新建--->构建一个自由风格的软件项目。

**接下来进入 Jenkins 配置页面。** 根据需要配置好相应的源码管理,触发器和环境后，添加构建，这里 Jmter 运行在 windows 中所以添加 windows shell，如果需要在 Linux 中构建可以选择对应 Shell，使用的命令如下图，该命令是跨平台的，不管 win,unix 还是 mac 都可以使用。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/23601da2e4b840e586d8abea1229cb1a.jpg)

- 命令格式为： -n -t <测试脚本文件路径> -l <日志保存路径> -e -o
- -n 非 GUI 模式 -> 在非 GUI 模式下运行 JMeter
- -t 测试文件 -> 要运行的 JMeter 测试脚本文件
- -l 日志文件 -> 记录结果的文件，文件必须不存在
- -e 设置测试完成后生成测试报表
- -o 指定报表生成文件夹，文件夹必须为空或者不存在
- -r 分布式远程执行 -> 指定远程slave节点执行测试任务
- -R 设置分布式服务器列表 -> 配置远程服务器ip列表
- -H 代理主机 -> 设置 JMeter 使用的代理主机
- -P 代理端口 -> 设置 JMeter 使用的代理主机的端口号

接下来配置构建后操作：

选择添加构建后操作，在列表中首先是选择 Publish HTML Reports。

选择增加。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/6ac875998dc64c668559fc029acb0078.jpg)

HTML directory to archive 中指定 Jmeter 生成报告的相对路径。

剩余配置一般默认就可以了。

接下来继续添加构建后操作，选择 Publish Performance test result report：

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/211868c5662f42d4a6fe866b6d0c1472.jpg)

- source data files：日志文件所在的相对路径。
- evaluation mode:选择判断模式，一般来说选择 standard mode (标准模式)， 标准模式中可以选择 relative threshold（相对阀值）或者 error threshold （相对错误数），这里因为是只执行一次的业务测试，所以选择 error threshold，并在下方配置1次测试失败就将 job 状态改为 failed。

如果是测试数量较多，且容忍一定的错误率可以选择 relative threshold，根据错误的百分比来修改 job 状态。

这里配置好以后就可以添加自己的后续部署任务了，再次选择增加构建后操作，选择 trigger parameterized build on other projects。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/77f7893ad3734a1581fb78ec9e499b9e.jpg)

填写测试结束后需要部署的项目，并选择 job 状态为什么时才会触发这个构建。

以上就是一个完整的根据测试结果自动部署任务了，接下来我们保存，选择立即构建。

**构建完成后的结果如图**

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/f1b465eccbd64239b0b5b2aef73f16bc.jpg)

选择 HTML report 可以查看 HTML 报告（如果使用新版本的 Jenkins，因为 Jenkins 的安全策略默认不加载 js，css 等文件，所以需要在 jenkins 设置中执行脚本命令行：System.setProperty("hudson.model.DirectoryBrowserSupport.CSP","sandbox; default-src 'self';")，执行完毕后重新构建就可以正常打开 HTML 报告。*这个命令重启后失效）

在 HTML 报告中可以看到测试结果的详细信息。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/81acb329ba844e5b857eeed23b5e4ecb.jpg)

首页是测试结果的总览，可以看到测试的错误率等，选择左边栏可以查看更详细些的请求时间，吞吐率等信息。

![img](http://5b0988e595225.cdn.sohucs.com/images/20170911/0abe9a72692e408f84ca5a77a9cc9636.gif)

配置一个自动化测试及部署任务就是这么简单，希望能够帮助到各位。

谢谢阅读。(●'◡'●)[返回搜狐，查看更多](http://www.sohu.com/?strategyid=00001&spm=smpc.content%2F64_2.content.2.1563268012334j3qws6w)