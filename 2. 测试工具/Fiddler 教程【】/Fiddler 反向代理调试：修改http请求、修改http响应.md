Fiddler 抓包调试


Fiddler 是一款功能强大的 http 抓包工具，对于 http 请求响应抓包调试十分实用，可以很方便查看 http 请求，http 响应的内容，修改 http 请求后重新发送，拦截 http 响应修改后重新响应；

 

Fiddler 基本界面介绍
以下是 fiddler 主界面的工具栏

![img](https://img-blog.csdn.net/20180718134613858?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsX2Fzc2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 

其中“切换代理模式”，fiddler 有以下 2 种代理模式：

缓冲模式：http 请求把所有数据缓冲结束后，再返回给客户端；

流模式：实时把服务器的数据返回给客户端；

 

请求响应调试工具栏常用组件面板介绍：

Statistics：显示当前用户选择的 Sessions 的汇总信息；
Inspectors：允许使用多种不同格式查看每个请求和响应的内容；
AutoResponder：将某一请求的响应结果替换成指定的资源；
Composer：创建自定义 http 请求，用于调试后端接口十分方便；
Filters：过滤器功能，用于对请求进行自定义规则过滤；
Timeline：用于分析请求响应时间，类似于 chrome network 调试面板的请求时间线；




Fiddler 反向代理调试


解决抓取 https 连接返回 443 问题
如果抓包请求为 https 请求，fiddler 会默认返回443端口无法解析的记录，类似如下：

![img](https://img-blog.csdn.net/20180718134640349?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsX2Fzc2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

进入【Tools -> Options -> HTTPS】, 勾选 Capture HTTPS traffic，Decrypt HTTPS traffic，之后重新启动 fiddler

![img](https://img-blog.csdn.net/20180718134702909?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsX2Fzc2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 

过滤 http 请求
进入 Filter 面板，勾选 Use Filters 选项，比如需要只显示“api.bilibili.com”主机下的请求，可以如以下设置：

![img](https://img-blog.csdn.net/20180718134720273?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsX2Fzc2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当然 Fileters 还有十分丰富的过滤配置，可以根据需求进行相应的请求过滤配置，如 Client process 可以对请求来源进行过滤，Request Headers 可以对请求头进行过滤，Response Status Code、Response Typeand Size 可以对响应状态码、响应类型进行配置；

 

修改重发 http 请求
Fillder 一个十分实用的功能，就是可以修改拦截到的 http 请求，修改请求参数后重新发送改请求，这个功能对于后端接口的调试十分方便（因为有些 http 请求需要验证 session，cookie 等数据，如果手动编写请求脚本，还原参数部分十分麻烦），特别是后端 web controller 相应的接口有比较复杂的条件判断分支，如果仅仅靠客户端界面去修改数据发送请求，有些判断分支的进入场景是十分难以再现的，使用这个功能可以直接修改请求参数，尽可能达到后端接口 controller 的每一个判断分支；

以下以更改 bilibili 视频详情页评论接口 https://api.bilibili.com/x/v2/reply?callback=jQuery17203964950070835611_1531888910878&jsonp=jsonp&pn=1&type=1&oid=26473757&sort=0&_=1531888915223，分页参数为例：

右键指定请求，点击 Unlock For Editing 

![img](https://img-blog.csdn.net/2018071813474171?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsX2Fzc2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

在 Inspectors 页中修改 Request Headers 中的分页 pn 请求参数（pn），因为该请求为 GET 请求，请求参数放置在 url 中，可以直接这样操作，如果是 POST 请求等把请求参数放置在请求正文中（表单形式、json、xml 等），可以在 TextView 或 SyntaxView 页中修改该请求参数；

![img](https://img-blog.csdn.net/20180718134757671?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsX2Fzc2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


右键该请求，选择  Replay -> Reissue Requests，即可获取该请求修改参数后返回的 http 响应；

![img](https://img-blog.csdn.net/20180718134812557?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsX2Fzc2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


修改重发 http 响应
同样的，Fiddler 也可以修改某个请求的响应正文，之后再该拦截该请求，自动将响应正文替换为修改后的响应正文，这个功能主要用在前端页面的调试上面，十分方便，无需后端接口编写某些特定的返回值，同时对比 chrome 自带的 source 工具，又具有跨页面调试的优势；

以下以修改bilibili 番剧详情页  https://bangumi.bilibili.com/ext/web_api/season_count?season_id=24572&season_type=1 http 响应正文中的番剧观看数量参数（views） ，验证前端字段渲染正确性为例；

解锁该请求，同修改 http 请求步骤一样，右键请求，选择 Unlock For Edit；
在 Inspectors 面板中，在响应部分的 SyntaxView 或 TextView 修改响应的参数；

![img](https://img-blog.csdn.net/20180718134830251?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsX2Fzc2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

直接拖动该请求到 AutoResponder ，勾选 Enable rules，Unmatcjed requests passthrough 

![img](https://img-blog.csdn.net/20180718134842652?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsX2Fzc2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


重新刷新浏览器页面，或者进行任何可以重新触发该请求的操作，可以在前端页面看到该响应数据被修改后的情况；

![img](https://img-blog.csdn.net/20180718134853512?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsX2Fzc2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



---------------------
