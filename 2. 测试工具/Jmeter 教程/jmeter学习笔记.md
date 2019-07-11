# jmeter学习笔记

2017年04月04日 16:03:35 [我要变大牛yeah](https://me.csdn.net/wuhenyan) 阅读数 3732



 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/wuhenyan/article/details/69071172

## 线程组

名称：随意设置，最好具有业务意义 
注释：随意设置，可以为空 
在取样器错误后要执行的动作：也就是其中一个请求出错后的异常处理方式 
继续，请求出错后继续运行，大量并发时，服务器出错是正常的事情，所以需要继续执行，记录出错作为性能依据 
Start Next Thread Loop：如果出错，同一脚本余下的请求不再执行，直接重新开始执行。比如，登录失败了，发帖的操作不再执行，重新开始下一轮迭代，从登录开始 
停止线程，如果出现请求失败，停止当前线程，不再执行；如果失败的事物增多，停下的线程也会增多，造成负载不够，一般不这么设置 
Stop Test Now：如果有线程请求失败了，立即停止测试场景 
线程属性： 
线程数：运行的线程数设置，一般一个线程对应一个模拟用户 
Ramp-up period：线程启动开始运营的时间间隔，单位是s。即所有线程在多长时间内开始运行。比如，设置线程数50，设置10s，那么每秒启动50/10个，如果设置为0，即开启场景后50个线程立即启动 
循环次数：请求的重复次数，选择forever，请求将一直继续除非停止或崩溃；如果不选forever，输入数字，请求将重复指定的次数 
Delay Thread creation until needed：勾选，线程在Ramp-up period的间隔时间启动并运行，比如50个线程10s的Ramp-up period时间，那么每隔一秒启动5个线程并运行后面的sampler。不勾选，测试计划开始后启动所有线程，但不立即运行sampler，是按照Ramp-up period时间来运行的。比如50个线程10s的Ramp-up period时间，那么计划开始后所有线程全部就绪，但第一秒只会启动5个线程并开始运营sampler

## 正则表达式提取器

名称：随意，最好具有业务意义 
注释：随意，可以为空 
应用范围： 
Main sample and sub-samples：匹配当前父取样器并覆盖子取样器 
Main sample only：仅匹配当前父取样器 
Sub-sample only：仅匹配子取样器 
Jmeter Variable：支持对jmeter变量进行匹配 
要检查的响应字段： 
主体：响应数据的主体部分，排除header；Http协议返回请求的主体部分就是body 
Body：针对替换了转义码的body部分 
Body as a Document：返回内容作为一个文档进行匹配 
信息头：只匹配信息头部分的内容 
URL：只匹配URL链接 
响应代码：匹配响应代码 
响应信息：匹配响应信息，比如处理成功返回“成功”或者“OK” 
引用名称：匹配出来的信息通过此名称进行访问，类似${引用明}进行访问 
正则表达式：使用此串进行信息匹配 
模板：正则表达式可以设置多个模板进行匹配，在此只可制定运营哪个模板，模板自动编号， \$1\$ 指第一个模板，\$2\$ 指第二个模板，以此类推，\$0\$ 指全文匹配 
匹配数字：在匹配时往出现多个值匹配的情况，如果数字为0代表随机取匹配值，不同模板可能会匹配一组，可以用匹配数字确定使用这一组值中的哪一个，负数取所有值，可以与for each controller一起使用来遍历 
默认值：如果没有匹配到可以制定一个默认值

注意：正则表达式中如果有? , 等特殊符号需要加\进行转义，否则提取不到正确的值；同时也要注意符号必须是英文格式；用beanshell输出变量时要用引号括起，否则报错

不填写模板：默认用${引用名称_g1}，${引用名称_g2}访问。。以此类推 
如下图，只匹配了一个模板，故g2 g3都未取到值 
![这里写图片描述](https://img-blog.csdn.net/20170404185648843?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](https://img-blog.csdn.net/20170404190140021?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
如下图，（beanshell同上面截图）匹配了两个模板，同样未填写模板编号，用${引用名称_g1}，${引用名称_g2}访问。g2取到值，g3不存在 
![这里写图片描述](https://img-blog.csdn.net/20170404190811575?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
以上两种不填写模板的情况，匹配结果只有一个值，所以匹配数字用1或者0，用其他数字都取不到值

如下三图，使用了模板，图一表示取第一个匹配，图二表示取第二个匹配，最终，这两种通过指定模板访问的方式都能取到结果 
![这里写图片描述](https://img-blog.csdn.net/20170404191525669?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](https://img-blog.csdn.net/20170404191537680?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](https://img-blog.csdn.net/20170404191549149?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

提取接口返回的json格式数据特定参数：注意这里因为取得是id字段的返回值，json格式还有很多其他字段，需要用逗号作为提取的边界，需要用\转义，否则不识别；与foreach结合使用，正则表达式提取器必须放在第一个http请求下，如果并列，foreach不进行循环（具体原因不明）；匹配数字需要输入负数，代表进行循环；通过debug sampler+查看结果数可以看到正则表达式返回的所有值以及可使用的所有变量，foreach控制器的变量前缀是正则表达式的引用明，start index输入0表示排除0从1开始，end index是${变量名_matchNr}，该变量通过debug可以看到，输出变量名作为后面请求引用的参数名

![这里写图片描述](https://img-blog.csdn.net/20170404192049891?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](https://img-blog.csdn.net/20170404192308786?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](https://img-blog.csdn.net/20170404192745666?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](https://img-blog.csdn.net/20170404192827620?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 简单控制器

用来指定一个执行单元，不改变元件的执行顺序，下面还可以嵌套其他的逻辑控制器

## Include Controller

导入外部的测试片段，被导入的测试计划不能有线程组，只能包含简单控制器及控制器下的元件。一个封装了业务的操作单元，类似函数一样 
文件名：必填，否则会报错

## Switch Controller

可以匹配数字或者字符，匹配数字的话是controller下面的请求编号，从0开始，依次递增，填数字几，表示匹配编号为几的请求；匹配字符时匹配取样器的名称，如果不匹配默认找名称为default的取样器执行，如果无则不执行任何取样器 
如下两图，匹配字符成功，执行对应的取样器 
![这里写图片描述](https://img-blog.csdn.net/20170405214344899?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](https://img-blog.csdn.net/20170405214318852?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
如下两图，匹配字符失败，执行名为default的取样器（在这里因为设置了http请求默认值，所以default请求有具体的地址） 
![这里写图片描述](https://img-blog.csdn.net/20170405214217715?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](https://img-blog.csdn.net/20170405214241351?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 交替控制器

## once only controller

仅一次控制器，此控制器下的元件只运行一次，即使把仅一次控制器放到循环控制器下面，也只运行一次。一般用于登录

## Throughput Controller

控制其下元件的执行次数，并无控制吞吐量功能，constant throughput timer可以控制吞吐量 
percent execution：按执行次数百分比计算执行次数，此时thoughput取值是0-100，per user是否勾选对percent execution模式无影响 
per user：勾选，按照虚拟用户数来计算执行次数，不勾选则是按照所有虚拟用户来计算执行次数 
total execution：按thouthput得值来制定执行次数，可以使任意整数，如果小于等于0一次也不执行，此时peruser与total execution一起影响执行次数

## if controller

通过某个条件控制其下的元件是否运行，condition可以使用js与变量表达式 
condition：判断条件，勾选interpret condition as vatiable expression，condition使用变量表达式来设置条件 
evaluate for each children：判断条件是否对所有子节点，默认不勾选，只在if控制器入口判断一次 
![这里写图片描述](https://img-blog.csdn.net/20170405222546957?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## random controller

随机控制器节点下的元件随机运行，与交替控制器不一样的是节点下元件的运行顺序不定 
ignore sub-controller blocks，忽略子控制器，即子控制器失效，由随机控制器接管

## random order controller

其下元件随机执行，不过每个元件只执行一次

## Loop controller

如果线程中设置了执行次数，那么循环控制器下面元件的执行次数是，线程组执行次数*循环控制器执行次数 
loop count：要么设置forever要么设置具体的次数

## transaction controller

事务控制器可以把下面取样器的执行消耗时间累加在一起，便于统计。同时对每个取样器的执行时间进行统计。 
如果事务控制器下有多个取样器，勾选他，那么在查看结果树中不仅可以看到事务控制器，还可以看到每个取样器；并且事务控制器定义的事物是否成功取决于子事物是否成功，其任何一个失败代表整个事物失败 
generate parent sample：选择是否生成一个父取样器结果，具体效果见下图，第一个事物的结果数是未勾选，第二个是勾选； 
![这里写图片描述](https://img-blog.csdn.net/20170405224437778?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
include duration of timer and pre-post processor in generated sample：是否包含定时器，选中的话在取样器前与后加上延迟，建议不勾选，否则统计还需扣除延迟

## random variable

能够生成随机数字并指定生成格式 
variable name：生成的随机数保存至此变量中 
outputformat：变量输出格式 
minimum value：随机数最小值 
maximum value：随机数最大值 
seed for random function：随机数种子 
per thread：生成的随机数是否在线程组中共享 
![这里写图片描述](https://img-blog.csdn.net/20170405225702957?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 计数器

启动：记录数量起始值 
递增：记录步长，1后是2，步长就是1 
最大值：记录的最大值 
number format：计数器格式，可以是数字，例如，000000（6位长度），000,000（6位，3位隔开）；字符加数字，例如xxx_000 
引用名称：计数器记录的值可以存入此变量中，供其他元件调用 
与每用户独立的跟踪计数器：每个线程都有自己的计数器，互不干扰 
reset counter on each thread group iteration：每次迭代复原计数器 
![这里写图片描述](https://img-blog.csdn.net/20170405230401225?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## login config element

用来配置默认的用户名和密码，有些sampler要验证用户名与密码，如果在sampler中没有配置用户名和密码会自动用该元件设置的填充

## constant throughput timer

固定吞吐量定时器，尽量保持吞吐量固定。如果吞吐量过大，增加迭代间的间隔时间，反之减少。增大或减少是由jmeter通过吞吐量，并发线程数来计算的 
this thread only：仅针对当前线程，也就是每个线程互不干扰，都分开计算要延迟多少时间 
all active thread：针对所有线程，把素有线程的吞吐量合并在一起作为因子计算 
all active thread in current thread group：针对当前线程组中的所有线程 
all active thread(shared) ：线程延迟计算是基于一个任意线程上次运行的时间，随机的取结果不一定准确，只能是努力控制在一个范围内 
all active thread in current thread group(shared) ：在当前线程组中取任意一个线程上次运行时间

## beanshell timer

设置线程延时间隔Thread.sleep（）； 
filename:脚本可以是从脚本文件中读取的 
Ctx：获取运行时信息ctx.getThreadNum() 
Vars：访问变量，获取对应值。也可以设置变量。vars.put(“key”,”value”) 
Props：直接访问jmeter属性，也可以设置属性值。 
props.get(“log_level.jmeter”) 
props.put(“log_level.jmeter”,”ERROR”) 
Prev：访问前面sampler的结果

beanshell preprocessor： 
reset interpreter：是否重新初始化 
parameters：脚本中变量初始化可以在这里指定，接受变量与字符串数组，如果是字符串数组，两个元素之间用空格隔开(猜测该变量指的应该是给scriptfile传递的参数) 
script file：指定运行的beanshell脚本 
Script：在此可直接编写beanshell脚本

regex user parameter： 
正则表达式用户变量，用来引用前一次正则表达式提取器提取的响应数据，是由取样器返回的。 
regular expression reference name：引用的正则表达式提取器中声明的变量名。 
parameter names and regexp group number：引用的变量名称 
parameter values regex group number：引用变量对应的值

用户参数： 
设置参数在取样器中进行参数化。用户（线程）在进行参数化取值时可以根据用户来区分。 
![这里写图片描述](https://img-blog.csdn.net/20170406223617237?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

HTTP URL 重写修饰符： 
重定向URL请求 
会话参数名称：URL重写的参数名称 
路径扩展：一般URL使用&做间隔，如果使用；就勾选 
do not use equal in path extension：参数和值一般使用=链接，如果不是勾选此项 
do not use ? in path extension：不适用?间隔参数 
cache sessionid ?:是否cache住sessionid供后续请求使用 
![这里写图片描述](https://img-blog.csdn.net/20170406224553173?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](https://img-blog.csdn.net/20170406224604975?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

debug postprocessor： 
jmeter properties：是否显示jmeter属性 
jmeter variables：是否显示jmeter变量 
sampler properties：是否显示取样器属性 
System properties：是否显示系统属性

JDBC postprocessor： 
实际上就是一个jdbc request，与其功能相同，都可以执行sql语句 
在测试时可能会有jdbc request修改了一些数据，测试完成后希望回复到原来状态，就可以用此元件，当然jdbc request也可以恢复数据

result status action handler： 
针对单一sampler设置运行时错误的处理逻辑 
继续：忽略错误，继续执行 
start next thread loop：停止当前迭代，开始下一迭代 
停止线程：停止当前线程 
停止测试：停止整个测试计划，当前迭代没有完成的线程完成后再停止 
stop test now：停止整个测试计划，立刻马上，没有完成的也停止

xpath extractor： 
xpath query中的表达式就是过滤字符，引用名称，后续可以引用的变量

beanshell assertion： 
可以访问： 
log对象，可以用此对象写日志 
SampleResult对象，从中可以获取响应数据，响应码等信息，只读？ 
Response对象，获取响应数据，只读 
Failure，用来设置断言成功与否，boolean类型 
FailureMessage，用来设置失败信息 
ResponseData对象，获取响应数据 
ResponseCode，响应码 
ResponseMessage，响应信息 
ResponseHeader，响应头信息 
RequestHeader，请求头信息 
SampleLabel，取样器label信息 
SamplerData，发送给服务器的数据 
Ctx，jmeter上下文信息，从中可以获取线程数，线程号等信息 
Vars，获取jmeter中定义的变量，或者设置变量 
Props，获取jmeter中的属性，或者设置属性 
![img](https://img-blog.csdn.net/20170406232729417?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
结果树中第一个是没有beanshell脚本中判断返回码一步，直接设置false的结果；第二个是设置true后的结果 
![结果树中](https://img-blog.csdn.net/20170406232741558?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VoZW55YW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
常用方法还有： 
getResponseDataAsString 
getResponseCode 
getResponseMessage

HTML assertion： 
支持HTML、XHTML、XML；主要是检查语法，error threashhold设置100表示在Jdity验证下少于100个错误与警告

size assertion： 
验证响应数据size大小 
断言应用范围： 
包括主sample与子sample；断言仅仅用于主sample；断言仅仅用于子sample；断言部分是jmeter变量。 
哪部分响应域参与断言： 
全部响应信息；响应头信息；响应主体内容部分；响应代码；响应信息，比如OK； 
断言时size的阈值设置

XML Schema assersion：校验文件是否是xml格式

xpath assersion： 
//string[text()=’吉林’] text()返回string标签对应的值

duration assertion： 
持续时间断言，验证取样器运行测试消耗掉的时间，可以针对主取样器与子取样器；时间单位是毫秒

监听器默认配置： 
bin/jmeter.properties中Results file configuration下设置的 
GUI方式设置： 
aggregate gragh：configuration弹窗中设置

aggregate gragh: 
可看到表格显示的结果与图形结果，图形结果记录的是响应时间。 
文件名：结果文件存储到磁盘的路径与文件名 
仅日志错误：是否仅记录错误事物 
success：记录成功事物 
configure：设置弹窗 
save gragh：保存图形结果 
columns to display：设置在图形结果中显示哪些结果字段 
foreground color：设置图形结果中文字的颜色 
value font：设置图形结果中文字的大小与样式 
column label selection：过滤箱，选择哪些请求在图形结果中显示，支持正则表达式

## 函数助手

选项—-函数助手对话框—-选择需要的函数—-输入参数—-点击生成—-将生成的使用方式粘贴至需要用到该参数的地方即可

## 访问地址参数化

将地址设置为用户定义的变量，在sampler中通过${VAR}使用，修改的时候也只需要修改用户定义的变量即可

## HTTP请求默认值

不同请求域名和端口等信息可能都是通用的，每一个请求都需要写一遍比较麻烦，可以设置成http请求默认值，具体http请求中不需要填写对应的字段，只需要填写其他不同的路径等内容即可，减少重复设置

## 响应断言

名称：随意设置，最好有业务意义 
注释：随意设置，可以为空 
应用范围： 
Main sample and sub-samples：匹配当前父取样器并覆盖子取样器 
Main sample only：仅匹配当前父取样器 
Sub-sample only：仅匹配子取样器 
Jmeter Variable：支持对jmeter变量进行匹配 
要测试的响应字段：针对响应数据的不同部分，分为以下七个， 
响应文本：响应服务器返回的文本内容，HTTP协议排除header部分 
Document：对文档内容进行匹配 
URL样本：匹配URL链接 
响应代码：匹配响应代码，比如HTTP协议返回码200代表成功 
响应信息：匹配响应信息，比如处理成功返回“成功”或者OK 
Response Header：匹配响应头信息 
Ignore Status：一个请求有多个响应断言，其中第一个响应断言选中此项，当第一个响应断言失败时可以忽略此响应结果，继续进行下一个断言，如果下一个断言成功则还是可以判定事物成功 
匹配模式规则： 
包括，响应内容包括需要匹配的内容即代表响应成功，支持正则表达式 
匹配，响应内容要完全匹配需要匹配的内容即代表响应成功，大小写不敏感，支持正则表达式 
Equal，响应内容要完全等于需要匹配的内容才代表成功，大小写敏感，需要匹配的内容是字符串正则表达式 
Substring，响应内容包含需要匹配的内容代表成功，大小写敏感，需要匹配的内容是字符串正则表达式 
否，选择Equal与Substring时匹配的是字符串，大小写敏感，有时会响应失败，此时选择否，会降低匹配级别，类似降到“包括”，“匹配”的级别，这样可以响应成功 
要测试的模式： 
填入需要匹配的字符串或者正则表达式，注意要与模式匹配规则搭配好

## Jmeter元件运行顺序

配置元件 
前置处理器 
定时器 
取样器 
后置处理器 
断言 
监听

## 性能参数配置

/bin/jmeter.bat 
set Heap：设置JVM堆大小，-Xms512m设置初始堆大小512M，-Xmx设置最大堆大小，还可以用-Xmn设置年轻代大小，官方建议年轻代可以是最大堆得3/8（也可以设置1/2） 
set NEW：设置年轻代大小，-XX:Newsize=128m，设置年轻代初始内存大小是128m，-XX:MaxNewSize=128m，设置年轻代最大内存大小是128m，-Xmn与-XX:MaxNewSize重复，为了方便，只用-Xmn设置即可，为了性能，一般会把xms与xmx设置一样大，避免年轻代初始内存沾满后扩充空间时内存中数据迁移导致的性能影响 
set SURVIVOR：-XX:SurvivorRatio=8设置suvivor与eden区大小的比值，那么就是survivor占用1/5的年轻代空间（新生代有2个survivor，即S0和S1，所以survivor总共占用新生代内存的2/（2+8），Eden与新生代的占比就为8/10）；-XX:TargetSurvivorRatio=50%，survivor区的实际使用率是50%，调整其占比率可以提高survivor区的利用率，最大90% 
set TENURING=-XX:MaxTenuringThreshold=2年轻代晋升老年代周期，即经过多少次GC还存活，默认是15 
set PERM=-XX:PermSize=64m 设置持久带初始大小是64m，-XX:MaxPermSize=128m设置持久带最大128m，-XX:+CMSClassUnloadingEnabled设置老年代CMS收集器对持久带进行垃圾回收，（CMS收集器默认不会对持久代进行垃圾回收，如果希望进行回收进行上面的设置） 
set DUMP=-XX:+HeapDumpOnOutOfMemoryError，设置当内存溢出时dump内存信息，方便JVM崩溃后方便查看堆信息进行问题分析，找到内存溢出原因