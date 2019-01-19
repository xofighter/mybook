restful接口
简单、直接、开发方便。利用现成的http协议
接口更加通用
会携带http的请求响应头，消息臃肿，会有无用的网络开销。
rpc

远程过程调用，更准确的说应该是一种协议应用，http是一种协议
速度快，效率高(针对http1.1)
更精简、更加保密、更加可定制
可以像调用本地接口一样使用 

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20180714111938433?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMzExOTg5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

但rpc限制了开发的语言环境

https://blog.csdn.net/qq_42311989/article/details/81041868

RPC框架一般都有注册中心，有丰富的监控管理

RPC框架一般是面向服务的封装。通常会封装一层服务发现和函数代理调用，针对服务的可用性和效率等都做了优化
一般会提供服务发现、负载均衡、水平扩展一整套分布式发现的机制

https://blog.csdn.net/ios_xumin/article/details/78315865

RPC的核心并不在于使用什么协议。rpc可以使用http来实现。

RPC的目的是让你在本地调用远程的方法，而对你来说这个调用是透明的，你并不知道这个调用的方法是部署哪里。通过RPC能解耦服务，这才是使用RPC的真正目的。 
https://www.cnblogs.com/winner-0715/p/5847638.html

如何选择
如果需要更高的传输效率，需要服务发现、水平扩展等相关技术，可以选用rpc
如果想要调用简单，接口更加通用，与编程语言无关，可以选用基于http的restful
--------------------- 
作者：阿睿93 
来源：CSDN 
原文：https://blog.csdn.net/designer01/article/details/82688863 
版权声明：本文为博主原创文章，转载请附上博文链接！