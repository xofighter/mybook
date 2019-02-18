## 1、User Fiters启用

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004215557318-1587287944.png)

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004215617927-1558972014.png)

## 2、Action

Action：Run Filterset now是否运行，Load Filterset加载，Save Filterset保存；

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004215736083-1739463428.png)

## 3、Hosts过滤

Zone：指定只显示内网（Intranet）或互联网（Internet）的内容；

Host：指定显示某个域名下的会话；

-No Host Filter：无HOST过滤；

Hide the following Hosts：只显示如下HOST；

Flag the following Hosts：加粗显示如下HOST；

输入多个HOST，多个之前用半角逗号或者回车分隔；

支持通配符：*,baidu.com；

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004220038490-957479275.png)

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004220047099-2027676117.png)

步骤：选择Hosts--->输入过滤条件单条件（ir.baidu.com）或多条件（ir.baidu.com,www.baidu.com或ir.baidu.com+Enter+www.baidu.com）或通配符（*.baidu.com）--->Changes not yet saved--->选择Ations中Run Filterset now；

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004220150693-846848458.png)

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004220206427-1732329327.png)

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004220217458-1855990795.png)

## 4、Client Process过滤

客户端进程过滤规则：

  Show only traffic from：你可以指定只捕获哪个Windows进程中的请求；

  Show only Internet Explorer traffic：只显示IE发出的请求；

  Hide Windows RSS platform traffic：隐藏Windows RSS平台发出的请求；

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004220357771-1964628074.png)

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004220414349-833463407.png)

## 5、Request Headers过滤

请求header过滤规则：

  经常使用：Show only if URL contains；

  Flag requests with headers：标记带有特定header的请求；

  Delete request headers：删除请求header；

  Set request header设置请求的header；

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004220516802-468891339.png)

## 6、Breakpoints

断点设置规则：

  Break request on HTTP POST：给所有POST请求设置断点；

  Break request on HTTP GET with QueryString：给所有带参数的GET请求设置断点；

  Break response on Content-Type：给特定的Content-Type设定断点；

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004220618693-751344101.png)

## 7、Response Status Code过滤

响应HTTP状态过滤规则：

  Hide success(202,204,206)：隐藏响应成功的session(202,204,206)；

  Hide Authentication demands(401)：隐藏未经授权被拒绝的session(401)；

  Hide redirects(300,301,302,303,307)：隐藏重定向的session(300,301,302,303,307)；

  Hide Not Modified(304)：隐藏无变更的session(304)；

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004220735911-1101918927.png)

## 8、Response Type and Size

响应类型和大小过滤规则：

  Show all Content-Type：显示所有响应类型；

  Hide smaller than ？KB：隐藏小于指定大小的session；

  Hide larger than ？KB：隐藏大于指定大小的session；

  Time HeatMap：获得即时数据（绿色阴影代表响应时间在50毫秒以内；超过50毫秒但在300毫秒之内的响应条目没有颜色；响应时间在300至500毫秒之间的会涂以黄色；超过500毫秒的用红色底纹显示）；

  Block script files：阻止脚本文件，显示为404；

  Block image files：阻止图片文件；

  Block SWF files：阻止SWF文件；

  Block CSS files：阻止CSS文件；

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004221026630-1573374574.png)

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004221044661-215943084.png)

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004221101083-789204519.png)

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004221120958-1994702363.png)

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004221143177-393365444.png)

## 9、Response Headers

响应header过滤规则：

  Flag response that set cookies：标记会设置cookie的响应；

  Flag response with headers：标记带有特定header的响应；

  Delete response headers：删除响应header；

  Set response header：设置响应的header；

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004221658365-1338290611.png)

## 10、会话列表：鼠标右击

![img](https://images2017.cnblogs.com/blog/1140207/201710/1140207-20171004221807833-1217194782.png)