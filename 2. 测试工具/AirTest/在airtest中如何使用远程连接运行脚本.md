# 在airtest中如何使用远程连接运行脚本

2018年04月13日 15:42:24 [C3程](https://me.csdn.net/C2496649361) 阅读数 1516



 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/C2496649361/article/details/79929883

step1： 
数据线连接手机，在cmd模式下输入：adb tcpip 5555 
此命令的目的是打开5555端口 
step2： 
查看手机IP地址一般会在关于手机–>状态消息的地方查看 
例如我的IP地址是192.168.0.1 
step3： 
在远程连接设备这里输入IP+端口号就可以了 
例如： 
192.168.0.1：5555 
![这里写图片描述](https://img-blog.csdn.net/2018041315380840?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0MyNDk2NjQ5MzYx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 
然后就可以了