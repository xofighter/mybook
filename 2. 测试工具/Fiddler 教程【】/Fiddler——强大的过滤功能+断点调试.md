> Fiddler 的过滤功能与断点调试是十分的有用的。

关于过滤功能：

![img](https://img-blog.csdn.net/20161009155016625?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

挨个介绍下这几个过滤的点。

1. Use Filters :是否使用过滤，只有在选择了这个选项以后才能修改下边的过滤条件，下边的过滤条件可以相互独立，也可以相互组合。

2. Actions  ：字面意思是动作的意思，也就是我们要做什么操作，里边包含有几个选项：
```
Run Filterset now :马上执行过滤

Load Filterset： 加载本地过滤设置文件

Save Filterset ：保存过滤条件到文件
```


3. Hosts：

通过主机名来进行过滤。
```
No  Zone  Filter :不通过空间进行过滤，这个是分内网跟外网的

show  only  Intranet  Hosts: 内网

show  only  internet   Hosts：外网
```

```
No  Host  Filter :不通过主机名进行过滤

Hide the following Hosts:隐藏下边输入的主机名的会话

show only  the following Hosts:显示下边输入的主机名的会话

flag  the following Hosts:标志下边输入的要过来的主机名的会话；
```
![img](https://img-blog.csdn.net/20161009160739665?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

黑色粗体的就是过滤出来的。

4. Client  Process 
根据客户端进程来进行过滤，选中 show only  traffic  from ,然后就可以选择要过来哪个进程的会话

![img](https://img-blog.csdn.net/20161009161635530?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


5. Request Headers：
根据request header的filter。这个可能是我觉得比较有用的一块儿了。

Show only if URL contains 和 Hide if URL contains 使用方法类似 功能相反。都是url中包含某些字符的filter，多种情况空格分隔。支持正则表达式和完全匹配，如下是个例子，可以简单理解为隐藏所有js和css请求。

 ![img](http://blog.mojijs.com/zb_users/upload/2015/10/201510251445742366125692.png)                

6. Breakpoints：

这个功能点要介绍的细点。（断点添加还可以在工具栏中Rules中进行设置）

关于断点调试的步骤：

（1）当我们在这个里边设置了断点过滤，那么就会在相应的会话请求中插入断点，例如下边我在get 请求中插入断点。那么可以发现在执行get 请求的会话是红色的图标。

![img](https://img-blog.csdn.net/20161009163205853?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)           

这个时候如果你要对其中某一条进行断点调试的话，那么就双击，然后就会显示这样一个界面：

![img](https://img-blog.csdn.net/20161009163431872?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

这个界面中的按钮的功能

![img](https://img-blog.csdn.net/20161009164245916?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

点击Break on Response 以后，你切换到下边的TextView选项卡里边，可以发现返回的数据，然后就可以进行修改

看实例：就以百度一下就知道界面吧。

打了断点以后，执行完Break on Response以后的数据：

![img](https://img-blog.csdn.net/20161009170548958?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


现在我对里边的  “百度一下”  这个按钮进行下修改，改成“Hello  welcome Fiddler”    

![img](https://img-blog.csdn.net/20161009171218486?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

7. Response Status  Code：

响应码过滤，就是隐藏相应状态码的会话，很简单，自己测试。

8. Response Type  and  Size：
这部分是根据响应类型和大小的filter，前一半是显示隐藏的filter，后block的filter。block的filter也挺有意思，如果你选中了block css那么浏览器就加载不到css了。

9. Response Headers：

是对response header的flag和修改。比较有用的是如果你想知道哪些请求修改了cookie那么就可以选中第一个checkbox。

前边的文章介绍过。

参考博客：http://blog.csdn.net/ohmygirl/article/details/17855031
