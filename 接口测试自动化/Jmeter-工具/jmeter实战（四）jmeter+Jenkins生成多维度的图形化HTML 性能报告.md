1、在jmeter.properties或者user.properties确认如下配置项：

> jmeter.save.saveservice.bytes = true
>
> jmeter.save.saveservice.label = true
>
> jmeter.save.saveservice.latency = true
>
> jmeter.save.saveservice.response_code = true
>
> jmeter.save.saveservice.response_message = true
>
> jmeter.save.saveservice.successful = true
>
> jmeter.save.saveservice.thread_counts = true
>
> jmeter.save.saveservice.thread_name = true
>
> jmeter.save.saveservice.time = true
>
> \# the timestamp format must include the time and should include the date.
>
> \# For example the default, which is milliseconds since the epoch:
>
> jmeter.save.saveservice.timestamp_format = ms
>
> \# Or the following would also be suitable
>
> jmeter.save.saveservice.timestamp_format = yyyy/MM/dd HH:mm:ss

如果希望在Errors报告中展示更详细数据，需要确保如下配置

> jmeter.save.saveservice.assertion_results_failure_message = true

如果使用了事务控制器(Transaction Controller)，确认*Generate parent sample*为未勾选状态

2.生成报告

Jmeter生成html报告的命令参数说明：

> -h 帮助 -> 打印出有用的信息并退出
>
> -n 非 GUI 模式 -> 在非 GUI 模式下运行 JMeter
>
> -t 测试文件 -> 要运行的 JMeter 测试脚本文件
>
> -l 日志文件 -> 记录结果的文件
>
> -r 远程执行 -> 启动all远程服务
>
> -R 远程执行 -> 启动指定远程服务
>
> -H 代理主机 -> 设置 JMeter 使用的代理主机
>
> -P 代理端口 -> 设置 JMeter 使用的代理主机的端口号
>
> -e 测试结束后，生成测试报告
>
> -o 指定测试报告的存放位置
>
> a. 在压力测试结束时报告

从cmd进入执行测试文件

基本命令格式：

> jmeter -n -t  <test JMX file>  -l  <test log file>-e -o  <Path to output  folder>

样例：

> jmeter -n -t d:\*.jmx  -l   d:\log.jtl   -e -o   d:\report



![img](https:////upload-images.jianshu.io/upload_images/3089283-5abe3125d4434de6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/965/format/webp)



b. 使用已有的压力测试CSV日志文件生成报告

基本命令格式：

> jmeter -g  -o

样例：

> jmeter -g D:\apache-jmeter-3.0\bin\testLogFile -o ./output



结合Jenkins自动构建



![img](https:////upload-images.jianshu.io/upload_images/3089283-b7dd4c6dec5c305a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/910/format/webp)







![img](https:////upload-images.jianshu.io/upload_images/3089283-71c9faa88f0eac9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/949/format/webp)



https://www.cnblogs.com/greattao/p/6813156.html

https://blog.csdn.net/smooth00/article/details/78728060

https://blog.csdn.net/huodoubi/article/details/60956124?utm_source=itdadao&utm_medium=referral

作者：芯W

链接：https://www.jianshu.com/p/8bde9595f7e1

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。