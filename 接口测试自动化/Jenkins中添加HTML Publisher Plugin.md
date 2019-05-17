由于jenkins中TestNG报告插件是xml格式,extentReports生成的报告是html格式。想要在jenkins上显示html报告，就需要在jenkins上安装HTML Publisher Plugin插件。安装过程略。

Jenkins Job配置
在Job中的【构建后操作】中选择Publish HTML reports，如图： 

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20171121190841695?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ2FyZV9zUXVlZW5kb20=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

选择后，【构建后操作】中就会出现Publish HTML reports配置 : 

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20171121191002412?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ2FyZV9zUXVlZW5kb20=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

点击【增加】: 

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20171121191100743?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ2FyZV9zUXVlZW5kb20=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


HTML directory to archive - html报告在项目中的路径，这个路径是相对于工作空间的。
Index page[s] - html文件名，包括后缀 其他设置默认就可以了。
Report title - 定义该插件在主页面中显示的链接名称。

至此，jenkins中添加HTML Publisher Plugin插件完毕。 
最后，点击【立即构建】，构建完成之后，左侧出现【HTML REPORT】入口，点击，就会显示html报告。

问题： 打开的html报告，没有加载css

解决： 在jenkins->系统管理->脚本命令行，输入:

System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "")
1
点击【运行】，就好了。 
但这只是暂时的，重启jenkins后，就会恢复默认设置。 
如果想永久解决这个问题 
1. 需要安装startup-trigger-plugin和Groovy插件。 
2. 在Job配置页面，构建触发器，勾选Build when job nodes start； 
3. 在Job配置页面，增加构建步骤Execute system Groovy script，在Groovy Command中输入上面命令，即可： 

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20171121195655625?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ2FyZV9zUXVlZW5kb20=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

---------------------
