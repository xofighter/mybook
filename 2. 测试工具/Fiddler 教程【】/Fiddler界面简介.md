Fiddler 祝界面布局如下：

![img](https://img-blog.csdn.net/20170204173719128?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvS0xBTEFMTA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

下面对界面上各具体栏目进行介绍。

一.fiddler工具栏
工具栏各功能介绍按照图标从左往右介绍如下图所示：

![img](https://img-blog.csdn.net/20170204174044810?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvS0xBTEFMTA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

二.websession 面板
web session 面板区域显示每条http请求的信息，各字段含义如下图所示：

![img](https://img-blog.csdn.net/20170204174105372?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvS0xBTEFMTA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

#号列中的图标，每种图标代表不同的相应类型，具体如下：

![img](https://img-blog.csdn.net/20170204174129950?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvS0xBTEFMTA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


三.QuickExec 命令
QuickExec 命令：可快速执行脚本命令

![img](https://img-blog.csdn.net/20170204174258701?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvS0xBTEFMTA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

四.详情和数据统计面板
    详情和数据统计面板，每条http请求的具体统计，下面是页签从左往右依次的说明。

1 Statistics 页签：显示当前用户的session的汇总信息，包括session总数，发送字节数，接受字节数，响应类型的汇总表，世界各地通过不同请求方式所需的时间等

底部图标标签可显示、复制和收起饼图等。

![img](https://img-blog.csdn.net/20170204174412670?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvS0xBTEFMTA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


2 Inspectors 页签：可使用不同格式查看请求和响应内容，上部显示发出的请求相关信息，下部显示接收的响应相关信息

![img](https://img-blog.csdn.net/20170204174440577?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvS0xBTEFMTA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)




请求（Request）部分字段详解：

![img](https://img-blog.csdn.net/20170204174543671?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvS0xBTEFMTA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

响应（Response）部分字段详解：

![img](https://img-blog.csdn.net/20170204174612905?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvS0xBTEFMTA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


3.autoresponse 页签：此功能启用后可将某一请求的响应结果替换成指定的资源（本地文件或fiddler内置的各种http响应），主要用于临时拦截某一请求的响应，而无需修改服务器上的环境和代码，保证在最真实的环境中进行调试，也无需再bug查找的时候就相关部门的配合。

启用该功能，请将 Enable automatic responses打勾。

· Permit passthroughfor unmatched requests 表示允许未匹配到的请求正常响应。应该打勾才能让其他的请求继续；否则其他未匹配到的请求都会以 404状态返回。

· 页签中间是一个列表，显示当前创建的匹配规则，左侧是匹配的条件，右侧是响应的结果。即：如果请求的地址包含左侧的字符串，那么就用右边设定的资源来替换来自服务器的响应。用 +和 - 两个按键可以调整当前选择规则在列表中的位置。

· 页签底部是一个规则编辑器，可以进行编辑当前用户选择的匹配规则、保存编辑、删除此匹配规则等操作。

4 composer页签:可模拟向服务器发送数据的过程（这就是灌水机器人的原理，也是部分http flood的一种方式），也可粘贴一次raw http header达到模拟请求的目的：

![img](https://img-blog.csdn.net/20170204174739609?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvS0xBTEFMTA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


5filter页签：过滤器功能，可分别对host过滤，客户端进程过滤，断点设置规则，请求和响应过滤等

![img](https://img-blog.csdn.net/20170204175036364?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvS0xBTEFMTA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


6 Timeline 页签：显示session请求到响应的时间表，横向为时间轴，纵向为session列表，鼠标移到timeline页签上，在底部会显示四个数据：session编号和URL、session的响应类型、发送字节数和接收字节数

![img](https://img-blog.csdn.net/20170204175127427?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvS0xBTEFMTA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


以上对fiddler界面信息已做了一些介绍，下面一篇对fiddler的一些具体实践进行说明。

---------------------
