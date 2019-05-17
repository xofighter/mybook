# 1. 简介

`Fiddler`（中文名称：小提琴）是一个`HTTP`的调试代理，以代理服务器的方式，监听系统的Http网络数据流动， `Fiddler`可以也可以让你检查所有的`HTTP`通讯，设置断点，以及`Fiddle`所有的“进出”的数据（我一般用来抓包）,`Fiddler`还包含一个简单却功能强大的基于`JScript .NET`事件脚本子系统，它可以支持众多的`HTTP`调试任务。

------

Fiddler官方网站提供了大量的帮助文档和视频教程,这是学习Fiddler的最好资料

- Fiddler_[官方网站](http://www.telerik.com/fiddler)
- Fiddler_[官方文档](http://docs.telerik.com/fiddler/configure-fiddler/tasks/configurefiddler)
- Fiddler_[官方视频](https://www.youtube.com/playlist?list=PLvmaC-XMqeBbw72l2G7FG7CntDTErjbHc)
- Fiddler_[官方插件](http://www.telerik.com/fiddler/add-ons)

------





# 2. 工作原理

![工作原理](http://ww2.sinaimg.cn/large/625782c8gw1elpk7nwv18j20nm09wgmi.jpg)

`Fiddler`是以代理WEB服务器的形式工作的,浏览器与服务器之间通过建立TCP连接以HTTP协议进行通信，浏览器默认通过自己发送HTTP请求到服务器，它使用代理地址:`127.0.0.1`, 端口:`8888`. 当`Fiddler`开启会自动设置代理， 退出的时候它会自动注销代理，这样就不会影响别的程序。不过如果`Fiddler`非正常退出，这时候因为`Fiddler`没有自动注销，会造成网页无法访问。解决的办法是重新启动下`Fiddler`.





# 3. HTTP协议简介

## 3.1 什么是HTTP协议

协议是指计算机通信网络中两台计算机之间进行通信所必须共同遵守的规定或规则，超文本传输协议(HTTP)是一种通信协议，它允许将超文本标记语言(HTML)文档从Web服务器传送到客户端的浏览器，目前我们使用的是HTTP/1.1 版本。

## 3.2 URL详解

`URL(Uniform Resource Locator)` 地址用于描述一个网络上的资源, 基本格式如下

```
schema://host[:port#]/path/.../[?query-string][#anchor]
```

| 名称         | 解释                                                         |
| ------------ | ------------------------------------------------------------ |
| scheme       | 指定低层使用的协议(例如：http, https, ftp)                   |
| host         | HTTP服务器的IP地址或者域名                                   |
| port#        | HTTP服务器的默认端口是80，这种情况下端口号可以省略。如果使用了别的端口，必须指明，例如 http://www.test.com:8080/ |
| path         | 访问资源的路径                                               |
| query-string | 发送给http服务器的数据                                       |
| anchor       | 锚                                                           |

## 3.3 HTTP消息的结构

- Request
  ![HTTP_1](http://ww1.sinaimg.cn/large/625782c8gw1elx0ltfr7mj20by05edg3.jpg)
  先看Request 消息的结构, Request 消息分为3部分，第一部分叫Request line, 第二部分叫Request header, 第三部分是body. header和body之间有个空行

第一行中的Method表示请求方法,比如"POST","GET", Path-to-resoure表示请求的资源， Http/version-number 表示HTTP协议的版本号,当使用的是"GET" 方法的时候， body是为空的
![HTTPHead_1](http://ww1.sinaimg.cn/large/625782c8gw1emair58kcjj20in0kf78d.jpg)

- Response
  ![HTTP_2](http://ww3.sinaimg.cn/large/625782c8gw1elx0q9770cj20by05ewen.jpg)
  我们再看Response消息的结构, 和Request消息的结构基本一样。 同样也分为三部分,第一部分叫Response line, 第二部分叫Response header，第三部分是body. header和body之间也有个空行
  ![HTTP_2](http://ww4.sinaimg.cn/large/625782c8gw1emair5tfn8j20k00ketf5.jpg)

## 3.4 状态码

Response 消息中的第一行叫做状态行，由HTTP协议版本号， 状态码， 状态消息 三部分组成。
状态码用来告诉HTTP客户端,HTTP服务器是否产生了预期的Response.
HTTP/1.1中定义了5类状态码， 状态码由三位数字组成，第一个数字定义了响应的类别

| 状态码 | 解释                                     |
| ------ | ---------------------------------------- |
| 1XX    | 提示信息，表示请求已被成功接收，继续处理 |
| 2XX    | 成功，表示请求已被成功接收，理解，接受   |
| 3XX    | 重定向，要完成请求必须进行更进一步的处理 |
| 4XX    | 客户端错误，请求有语法错误或请求无法实现 |
| 5XX    | 服务器端错误，服务器未能实现合法的请求   |

- 200 OK
  最常见的就是成功响应状态码200了， 这表明该请求被成功地完成，所请求的资源发送回客户端
  ![StateCode_1](http://ww2.sinaimg.cn/large/625782c8gw1emair6avj9j20k90hf78b.jpg)
- 302 Found
  重定向，新的URL会在response 中的Location中返回，浏览器将会自动使用新的URL发出新的Request

例如在IE中输入， http://www.google.com. HTTP服务器会返回302， IE取到Response中Location header的新URL, 又重新发送了一个Request.
![StateCode_2](http://ww3.sinaimg.cn/large/625782c8gw1emair6wcxyj20od0gs0xe.jpg)

- 304 Not Modified
  代表上次的文档已经被缓存了， 还可以继续使用，例如打开博客园首页, 发现很多Response 的status code 都是304
  [提示：　如果你不想使用本地缓存可以用Ctrl+F5 强制刷新页面]
  ![StateCode_3](http://ww3.sinaimg.cn/mw690/625782c8gw1emair7mre2j20l40eugqe.jpg)
- 400 Bad Request 客户端请求与语法错误，不能被服务器所理解
- 403 Forbidden 服务器收到请求，但是拒绝提供服务
- 404 Not Found
  ![StateCode_4](http://ww4.sinaimg.cn/mw690/625782c8gw1emair8nbagj20k50lsaek.jpg)
- 500 Internal Server Error 服务器发生了不可预期的错误
- 503 Server Unavailable 服务器当前不能处理客户端的请求，一段时间后可能恢复正常





# 4. 界面操作

## 4.1 主界面

![主界面](http://ww4.sinaimg.cn/large/625782c8gw1elq2t384r5j21aw0rok9a.jpg)

`Fiddler`的主界面分为 工具面板、会话面板、监控面板、状态面板，下面进行一一介绍。

## 4.2 工具面板

![工具面板](http://ww4.sinaimg.cn/large/625782c8gw1elq2p3w79lj20zd00w0tj.jpg)

- 说明
  注释、重新请求、删除会话、继续执行、流模式/缓冲模式、解码、保留会话、监控指定进程、寻找、保存会话、切图、计时、打开浏览器、清除IE缓存、编码/解码工具、弹出控制监控面板、MSDN、帮助
- 两种模式

缓冲模式（`Buffering Mode`）
`Fiddler`直到`HTTP`响应完成时才将数据返回给应用程序。可以控制响应，修改响应数据。但是时序图有时候会出现异常

流模式（`Streaming Mode`）
`Fiddler`会即时将`HTTP`响应的数据返回给应用程序。更接近真实浏览器的性能。时序图更准确。但是不能控制响应。

![会话面板](http://ww1.sinaimg.cn/mw690/625782c8gw1elqz09yarbj20n708zwf0.jpg)

## 4.3 会话面板

![会话面板](http://ww2.sinaimg.cn/large/625782c8gw1elq2gi01rsj2163078tfz.jpg)

![会话面板图标](http://ww1.sinaimg.cn/mw690/625782c8gw1elqy4z4qmqj20at096gmd.jpg)

## 4.4 监控面板

![监控面板](http://ww3.sinaimg.cn/large/625782c8gw1elq2j9fxrkj20n10ow7bo.jpg)

- 统计报表

(1) 请求总数、请求包大小、响应包大小；

(2) 请求起始时间、响应结束时间、握手时间、等待时间、路由时间、`TCP/IP`传输时间；

(3) `HTTP`状态码统计；

(4) 返回的各种类型数据的大小统计以及饼图展现。
![统计报表](http://ww2.sinaimg.cn/large/625782c8gw1elpm8svukoj215n0p0tf2.jpg)

- 时间轴
  每个网络请求都会经历域名解析、建立连接、发送请求、接受数据等阶段。把多个请求以时间作为 X 轴，用图表的形式展现出来，就形成了瀑布图。在`Fiddler` 中，只要在左侧选中一些请求，右侧选择`Timeline`标签，就可以看到这些请求的瀑布图

![时间轴](http://ww3.sinaimg.cn/mw690/625782c8gw1elquvn14bbj20go05iweu.jpg)

看到这张图，你是否可以回答这些问题：

1. 图标的 Y 轴上显示的是简化后的 URL。为什么有些是绿色的，有些是黑色的？
2. 为什么第一个请求用阴影线来表示，其它请求却都是实心的？
3. 请求条的不同颜色分别代表什么？
4. 每个请求中的黑色竖线表示什么？
5. 请求后面的图标（如闪电和软盘）代表了什么？
6. 每个请求前面两个小圆圈是什么，为什么有的是红色，有的是绿色？
   下面将一一揭晓。

------

1. 绿色的请求表示这是一个“有条件的请求”。HTTP 协议定义了 5 个条件请求头部，最常见的两个是“If-Modified-Since”和“If-None-Match”。服务器根据这两个头部来验证本地缓存是否过期，如果过期则正常返回资源的最新版本；否则仅返回 304 Not Modified，浏览器继续使用本地缓存。包含条件请求头部的请求用绿色显示，否则用黑色。
2. 有阴影线的请求是缓冲模式下的请求，实心的是流模式下的请求。Fiddler 提供了缓冲（Buffering）和流（Streaming）两种抓包模式：缓冲模式下，Fiddler 会在响应完成时才将数据返回给应用程序（通常是浏览器），这种模式下可以控制响应，方便地修改响应内容；流模式下，Fiddler 会实时返回响应数据给浏览器，但没办法控制响应。一般使用流模式，瀑布图会更真实一些。这两种模式可以通过 Fiddler 的工具栏选择。特别的，通过 Fiddler 的“AutoResponder”功能返回的响应，只能是缓冲模式。
3. 请求条的不同颜色对应着不同类型的响应，根据响应头的 MIME Type 来归类。如浅绿色表示图片类型的响应；深绿色是 JavaScript；紫色是 CSS；其它都是蓝色。
4. 请求中的黑色竖线，表示的是浏览器收到服务端响应的第一个字节这一时刻。这个时间受 DNS 解析、建立连接、发送请求、等待服务端响应等步骤的影响。
5. 请求条后面的图标表示响应的某些特征。如软盘图标表示这个响应正文从本地获得，也就是说服务端返回了 304；闪电表示这是 Fiddler 的“AutoResponder”的响应；向下的箭头表示响应是 302，需要重定向；红色感叹号说明这个请求有错误发生（状态码是 4XX 或 5XX）。特别的，如果请求条后面有一个红色的X，说明服务端响应完这个请求之后，断开了连接。出现这种情况一般有两种可能：HTTP/1.0 的响应中没有 Connection: Keep-Alive；或者是 HTTP/1.1 的响应中包含了 Connection: close。使用持久连接可以省去建立连接的开销，也可以减小 TCP 慢启动和其它拥塞控制机制带来的影响，总之是好处多多。
6. 请求前面的红色圆圈表示这个连接是新建的，绿色表示是复用的。上面的圆圈表示的是浏览器到 Fiddler 的连接，下面的圆圈是 Fiddler 到服务端的连接。

## 4.5 状态面板

![状态面板](http://ww1.sinaimg.cn/mw690/625782c8gw1elq2kojlyoj20nc01nq3d.jpg)

- 控制台
  Fiddler的左下角有一个命令行工具叫做QuickExec,允许你直接输入命令。
  常见得命令有

| 命令    | 解释                                         |
| ------- | -------------------------------------------- |
| help    | 打开官方的使用页面介绍，所有的命令都会列出来 |
| cls     | 清屏 (Ctrl+x 也可以清屏)                     |
| select  | 选择会话的命令                               |
| ?.png   | 用来选择png后缀的图片                        |
| bpu     | 截获request                                  |
| bpafter | 截获response                                 |





# 5. 常用功能

## 5.1 监听HTTPS

Fiddler不仅能监听HTTP请求而且默认情况下也能捕获到HTTPS请求，Tool -> Fiddler Option -> HTTPS下面进行设置，勾选上“Decrypt HTTPS traffic”，如果不必监听服务器端得证书错误可以勾上“Ignore server certification errors”，也可以跳过几个指定的HOST来缩小或者扩大监听范围。

HTTPS例子：
https://pay.tenpay.com/main/app/v1.0/trans_manage.cgi?OutPutType=JSON

![对比文本](http://ww3.sinaimg.cn/mw690/625782c8gw1elqwqsvo63j20fd0a8dh7.jpg)

## 5.2 HOST切换

![HOST](http://ww2.sinaimg.cn/mw690/625782c8gw1elxmd4jcc3j20jd0ddn24.jpg)

## 5.2 模拟各类场景

- 通过GZIP压缩，测试性能
- 模拟Agent测试，查看服务端是否对不同客户端定制响应
- 模拟慢速网络，测试页面的容错性
- 禁用缓存，方便调试一些静态文件或测试服务端响应情况
- 根据一些场景自定义规则
  ![对比文本](http://ww4.sinaimg.cn/mw690/625782c8gw1elqyjdzz1aj212b0lcqbw.jpg)
- 低网速模拟
  有时出于兼容性考虑或者对某处进行性能优化，在低网速下往往能较快发现问题所在也容易发现性能瓶颈，可惜其他调试工具没能提供低网速环境，而强大的Fiddler考虑到了这一点，能够进行低网速模拟设置Rules > Performance > Stimulate Modem Speeds。

## 5.3 Compare（对比文本）

![对比文本](http://ww4.sinaimg.cn/mw690/625782c8gw1elqvkwthjgj20uf0n1jw8.jpg)

## 5.4 Composer（构造器）

请求构造顾名思义就是我们可以模拟请求，也就是说我们可以借助Fiddler的Composer 在不改动开发环境实际代码的情况下修改请求中的参数值并且方便的重新调用一次该请求，然后相比较2次请求响应有何具体不同。任何一个请求参数只要是合法的取值再次调用后都会有相应的响应，那么你想要的任意一个合法请求组合自然也能够按照你的意愿构造出来，然后再次调用以及查看返回数据，十分方便！

下面举一个交易查询请求构造的例子。首先进入交易查询页面抓包找到目标请求https://pay.tenpay.com/main/app/v1.0/trans_manage.cgi?OutPutType=JSON，双击该包在Inspectors标签下查看返回数据为JSON格式，而XML格式一栏为空：
![构造器_1](http://ww4.sinaimg.cn/mw690/625782c8gw1elqz7rbhp5j20bi0730t5.jpg)

将该请求鼠标左键单击拖入Fiddler右侧Request Builder标签内并修改原请求参数OutPutType=JSON为OutPu tType=XML，然后点击Execute按钮再次触发调用请求，
![构造器_2](http://ww2.sinaimg.cn/mw690/625782c8gw1elqz7rqtpcj20bm0333yv.jpg)

双击这次请求包在Inspectors标签下查看返回数据为XML格式，而JSON格式一栏为空：
![构造器_3](http://ww1.sinaimg.cn/mw690/625782c8gw1elqz7s3o0fj20bj070q3c.jpg)

## 5.5 Filters(过滤监控)

对一个重新载入的页面进行抓包，如果包的条目过多而你需要关注的就那么几项的话，可以使用Fiddler的过滤器Filters进行抓包，那么抓包时只会抓取你希望抓到的那些包。切换到Filters标签勾选Use filter，以便激活过滤器，这样下面的各种过滤方式就可以进行选择了。

(1).
![Filter_1](http://ww3.sinaimg.cn/mw690/625782c8gw1elqxfq4wl8j20gi06ljsd.jpg)

(2).
![Filter_2](http://ww4.sinaimg.cn/mw690/625782c8gw1elqxfps7xwj20e404074n.jpg)

| 选项1                    | 解释             |
| ------------------------ | ---------------- |
| No zone filter           | 不设置hosts过滤  |
| Show Only Intranet Hosts | 只显示内网HOST   |
| Show Only Internet Hosts | 只显示外网HOST令 |

| 选项2                         | 解释               |
| ----------------------------- | ------------------ |
| No Host Filter                | 不设置hosts过滤    |
| Show Only The Following Hosts | 隐藏过滤到的域名   |
| Show Only The Following Hosts | 只显示过滤到的域名 |
| Flag The Following Hosts      | 标记过滤到的域名   |

## 5.5 AutoResponder(请求重定向)

所谓请求无非就是需要调用到的一些资源(包括JS、CSS和图片等)，所谓重定向就是将页面原本需要调用的资源指向其他资源(你能够控制的资源或者可以引用到的资源)。

(1)你可以将前台服务器的诸多或者某个资源在本地做个副本，如果正常网络访问环境下该资源出现了BUG而导致开发环境崩溃时，可以先将这个资源的请求重定向到本地副本，这样就可以继续进行开发调试你的页面，从而大量节省资源维护的等待时间。

(2)你也可以将多人同时维护的某个JS文件复制一份出来在本地，当你的开发调试收到他人调试代码干扰时，可以将这个JS的调用重定向到本地无干扰的JS文件，进行无干扰开发，功能开发完成并调试OK之后再将你的代码小心合入到开发环境中，这样就可以避免受到他人干扰专心搞你的模块开发，也就是说能够将JS文件脱离开发环境却不影响线上调试。

(3)你还可以将样式文件或者图片指向本地如果需要的话。开发过程中的很多页面其实都是惨不忍睹的，究其原因很大程度上是因为缺少对应的样式文件或者没有图片资源，所以样式文件和图片的重定向会对美感稍有要求的开发人员带来福音。

![重定向](http://ww4.sinaimg.cn/mw690/625782c8gw1elqy1v4k8qj20ds051my1.jpg)





# 6. 插件介绍

## 6.1 Format

![Format_1](http://ww1.sinaimg.cn/mw690/625782c8gw1elxlq1u81sj20nj0akdp3.jpg)

![Format_1](http://ww3.sinaimg.cn/mw690/625782c8gw1elxlq2kt51j20nk0ao0vr.jpg)

## 6.2 Script

- Fiddler Script 是用JScript.NET语言写的，
  [JScript.NET](http://msdn.microsoft.com/zh-cn/library/cc435359(VS.71).aspx)
- 在这个方法中修改Request的内容， 我们用得最多,

```
static function OnBeforeRequest(oSession: Session)
```

- 在这个方法中修改Response的内容，

```
static function OnBeforeResponse(oSession: Session)
```

- 添加IP Main方法中添加

```
FiddlerObject.UI.lvSessions.AddBoundColumn("HostIP", 50, "x-hostIP");
```

- 请求，响应延迟 在OnBeforeRequest 添加

```
oSession["request-trickle-delay"]  = "3000"; 
oSession["response-trickle-delay"] = "3000";
```

- 我们可以控制Session在Fiddler中显示的样式，把这段脚本放在OnBeforeRequest(oSession: Session) 方法下，并且点击"Save script", 这样所有的cnblogs的会话都会显示红色.

```
  if (oSession.HostnameIs("www.cnblogs.com")) {
            oSession["ui-color"] = "red";
        }
```

- Fiddler Script中修改Cookie
  Cookie其实就是request 中的一个header,注意:FiddlerScript不能直接删除或者编辑单独的一个cookie， 你需要用replace方法或者正则表达式的方法去操作cookie的string

```
static function OnBeforeRequest(oSession: Session) 
{ 
     if (oSession.HostnameIs('www.example.com')
       && oSession.uriContains('pagewithCookie') 
       && oSession.oRequest.headers.Contains("Cookie")) 
     { 
         var sCookie = oSession.oRequest["Cookie"]; 
         
         // 用replace方法或者正则表达式的方法去操作cookie的string
         sCookie = sCookie.Replace("cookieName=", "ignoreme="); 
         oSession.oRequest["Cookie"] = sCookie; 
    }
```

- 删除所有的cookie

```
oSession.oRequest.headers.Remove("Cookie");
```

- 新建cookie

```
oSession.oRequest.headers.Add("Cookie", "username=testname;testpassword=P@ssword1");
```

- Fiddler Script中修改Request 中的body

```
static function OnBeforeRequest(oSession: Session) 
{ 
    if(oSession.uriContains("http://www.cnblogs.com/"))
    {
        // 获取Request 中的body字符串
        var strBody=oSession.GetRequestBodyAsString();
        
        // 用正则表达式或者replace方法去修改string
        strBody=strBody.replace("1111","2222");
        
        // 弹个对话框检查下修改后的body               
        FiddlerObject.alert(strBody);
        
        // 将修改后的body，重新写回Request中
        oSession.utilSetRequestBody(strBody);
    }
}
```

VS插件：https://visualstudiogallery.msdn.microsoft.com/872d27ee-38c7-4a97-98dc-0d8a431cc2ed

## 6.3 三方插件

- .NET可以开发
- 插件管理
  ![IE](http://ww1.sinaimg.cn/mw690/625782c8gw1elxmgvvx07j20fi0ad77q.jpg)





# 7. 浏览器抓包方式

## 7.1 IE

![IE](http://ww3.sinaimg.cn/large/625782c8gw1elxk7795z1j20mv0k9n1s.jpg)

## 7.2 Firefox

![Firefox](http://ww4.sinaimg.cn/large/625782c8gw1elxk76hc62j20v00cudl8.jpg)

## 7.3 Chrome

![Chrome](http://ww2.sinaimg.cn/large/625782c8gw1elxk75frokj20hx0f6ac4.jpg)

## 7.4 VS

我们在用visual stuido 开发ASP.NET网站的时候也需要用Fiddler来分析HTTP， 默认的时候Fiddler是不能嗅探到localhost的网站。

在localhost后面加个点号，Fiddler就能嗅探到。

例如：原本ASP.NET的地址是 http://localhost:2391/Default.aspx， 加个点号后，变成 http://localhost.:2391/Default.aspx 就可以了
![VS](http://ww2.sinaimg.cn/mw690/625782c8gw1elxke97x0zj20gk0da40f.jpg)





# 8. 移动端抓包

Fiddler不但能截获各种浏览器发出的HTTP请求, 也可以截获各种智能手机发出的HTTP/HTTPS请求。

Fiddler能捕获IOS,Andriod,WinPhone,设备发出的请求，同理，也可以截获IPad, MacBook的等设备发出的HTTP/HTTPS。

前提条件是：安装Fiddler的机器，跟Iphone 在同一个网络里， 否则IPhone不能把HTTP发送到Fiddler的机器上来。

具体操作步骤如下：

- Fiddler设置
  打开Fiddler, Tools-> Fiddler Options。（配置完后记得要重启Fiddler）.

选中"Allow remote computers to connect". 是允许别的机器把HTTP/HTTPS请求发送到Fiddler上来
![APP](http://ww1.sinaimg.cn/mw690/625782c8gw1elxkl6mft1j20fi0ad406.jpg)

- 获取Fiddler所在机器的IP
- 安装Fiddler证书
  这一步是为了让Fiddler能捕获HTTPS请求。 如果你只需要截获HTTP请求， 可以忽略这一步

首先要知道Fiddler所在的机器的IP地址：　假如我安装了Fiddler的机器的IP地址是:192.168.1.104
打开IPhone 的Safari, 访问 http://192.168.1.104:8888， 点"FiddlerRoot certificate" 然后安装证书

![APP_1](http://ww1.sinaimg.cn/mw690/625782c8gw1elxkp6jhrej20ck0aamyf.jpg)

![APP_2](http://ww1.sinaimg.cn/mw690/625782c8gw1elxkp76ia2j20cl0eimyz.jpg)

- 打开IPhone, 找到你的网络连接， 打开HTTP代理， 输入Fiddler所在机器的IP地址(比如:192.168.1.104) 以及Fiddler的端口号8888
  ![APP_3](http://ww3.sinaimg.cn/mw690/625782c8gw1elxkp7py7dj20cp09bmy3.jpg)





# 9. 推荐书籍

- 《Fiddler调试权威指南》是Fiddler的开发者Eric Lawrence编写的一本权威的参考指南。全书分为10章和4个附录，从认识Fiddler开始，介绍了基本技巧和概念、配置选项、Inspectors、扩展、数据流导入导出、FiddlerScript和FiddlerCore等主题；附录部分还给出了故障排除和命令行等有用的参考信息。
- 《Fiddler调试权威指南》适合Web开发人员和Web测试人员阅读参考，也适合想要学习和掌握Fiddler的读者阅读。通过《Fiddler调试权威指南》，你将学会如何利用Fiddler调试Web相关的应用，掌握如何调试HTTPS数据流，学会如何在流行的设备上使用Fiddler，甚至掌握更多高级的扩展功能。





# 10. 其他抓包工具

同类的工具有 : HttpWatch, Firebug,Chrome自带调试工具，WireShark





# 11. 总结

```
通过以上的介绍，你应该已经发现fiddler其它强大的功能。fiddler绝对是开发利器。
```

- Fiddler能记录所有客户端和服务器的`HTTP`和`HTTPS`请求，允许你监视、设置断点，甚至修改输入输出数据，Fiddler包含了一个强大的基于事件脚本的子系统，并且能使用`.NET`语言进行扩展；
- 你对`HTTP`协议越了解，你就能越掌握Fiddler的使用方法，你越使用Fiddler,就越能帮助你了解`HTTP`协议，两者关系紧密、相辅相成；
- 强大图形呈现，拖拽操作，丰富的插件。
- 使用Fiddler无论对`Coder`还是`Tester`来说，都是非常有用的工具。