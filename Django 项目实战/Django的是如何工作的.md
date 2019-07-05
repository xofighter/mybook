## [Django的是如何工作的](https://www.cnblogs.com/fnng/p/4373108.html)

2015-03-27 23:17 by 虫师, 24667 阅读, 6 评论, [收藏](https://www.cnblogs.com/fnng/p/4373108.html#), [编辑](https://i.cnblogs.com/EditPosts.aspx?postid=4373108)

　　如果你看过我之前所写的关于django的文章的话，你会发现每一篇都具有可操作性，都是从创建项目开始的，虽然中间之加了一些要讲解的重点。这也是我博文的特点，我希望在你看到我这一篇文章的时候是可操作的，不管是否具备了相关基础。

如果你是第一次接触django，建议参考我的之关于django的内容练习一下：

http://www.cnblogs.com/fnng/category/581256.html

 

这一篇要介绍django是如何工作的。如果你把这过程梳理清晰了，那么你对django就算入门了。

 

一张流程图告诉你，django的处理流程：

![img](https://images0.cnblogs.com/blog2015/311516/201503/272244545369594.jpg)

 

# URL组成                                  

 

　　作为网站的用户，我们首先在浏览器的输入框内输入：http://127.0.0.1:8000/index/

 ![img](https://images0.cnblogs.com/blog2015/311516/201503/272246185363057.jpg)

URL地址由以下几部分组成：

**协议类型**： HTTP ，FTP 

　　HTTP协议（HyperText Transfer Protocol，超文本传输协议）是用于从WWW服务器传输超文本到本地[浏览器](http://baike.baidu.com/view/7718.htm)的传送协议。它可以使浏览器更加高效，使网络传输减少。它不仅保证计算机正确快速地传输超文本文档，还确定传输文档中的哪一部分，以及哪部分内容首先显示等 。

HTTPS（全称：Hyper Text Transfer Protocol over Secure Socket Layer），是以安全为目标的[HTTP](http://baike.baidu.com/view/9472.htm)通道，简单讲是HTTP的安全版。

 

**主机地址**：itest.info  ，127.0.0.1

前者是一个网址，网址通过域名解析服务器会找到对应的IP地址。后者就是一个IP地址。

127.0.0.1 指向的就是本机的IP地址。

 

**端口号**： 8000

端口号用于区分标识同一台主机的不同应用。一台主机上有很多应用，你访问我这台主机时如果指定的是8000，那么就是知道是你来访问我用django开发的blog的。当然，这个端口是可以任意分配的。

 

**路径** ： /index/  、/admin

一般用来表示主机上的一个目录或文件地址。

 

 

# urls的配置                                 

 

　　django通过urls.py配置文件很好的处理了前端请求的指向，其中使用使用Python的正则表达式可以使匹配变得更灵活。

打开django下面的urls.py文件：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
from django.conf.urls import patterns, include, url
from django.contrib import admin

urlpatterns = patterns('',
    url(r'^admin/', include(admin.site.urls)),
    url(r'^index/$', 'blog.views.index'),
)
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 r'^index/$'

这是一个使用了python的正则表达式。

字符串有前面加“ r ”是为了防止字符串中出现类似“\t”字符时被转义。

 

　　django在拿到URL地址后，取端口号后面的文件夹路径（/index/）进行配置，结果^index/$ 可以对这个文件路径进行匹配。那么将指向blog.views.index 这个地址。

blog是文件夹，views是一个.py文件，index 则是函数名。

![img](https://images0.cnblogs.com/blog2015/311516/201503/272249531451489.jpg)

 

 



# model模型                                

 

　　Django用模型在后台执行SQL代码并把结果用Python的数据结构来描述。Django也使用模型来呈现SQL无法处理的高级概念。

　　模型用于数据库的创建，在settings.py文件中配置数据库的连接,例如一个mysql数据库的配置：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
……
DATABASES = {
    'default': {
        'ENGINE' : 'django.db.backends.mysql',
        'HOST' : '127.0.0.1',
        'PORT' : '3306',
        'NAME' : 'myweb',
        'USER' : 'user',
        'PASSWORD' : '123456',
    }
}
……
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 　　配置信息从上到下依次是驱动（ENGINE），主机地址（HOST），端口号（PORT），数据库（NAME），登录用户名（USER），登录密码（PASSWORD）。

　　在应用的的models.py文件中创建模型，为了避免直接操作数据库，通过创建模型去生成对应的数据库表。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
from django.db import models
# Create your models here.

class BlogsPost(models.Model):
    title = models.CharField(max_length = 150)
    body = models.TextField()
    timestamp = models.DateTimeField()
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

　　执行数据库同步会创建一张PlogsPost表，表分别会有title、body、timestamp三个字段。其中title定义为char类型，定义最长150字符；body为text文本类型；timestamp为日期时间类型。

　　我们不用关心到底怎么创建表，只要创建好模型就好了，剩下的由djnago来帮我们生成对应的表。下面是将模型创建成数据库表的命令：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
D:\pydj\myweb>python manage.py makemigrations blog
Migrations for 'blog':
  0001_initial.py:
- Create model BlogsPost
D:\pydj\myweb>python manage.py syncdb
Operations to perform:
  Apply all migrations: admin, blog, contenttypes, auth, sessions
Running migrations:
  Applying blog.0001_initial... OK
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

　　首先是通过 makemigrations对某个应用（blog）下的模型执行迁移；然后通过syncdb检测项目下的所有模型，发现模型有变动或未生成表，将重新生成相应的表。（在djnaog 1.7之前的版本只需要syncdb一个命令就可以完成同步）

 

 

# views视图                                

 

 　　视图可以看作是前端与数据库的中间人，他会将前端想要的数据从数据库中读出来给前端。他也会将用户要想保存的数据写到数据库。

views.py

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
#coding=utf-8
from django.shortcuts import render
from blog.models import BlogsPost
from django.shortcuts import render_to_response

# Create your views here.
def index(request):
    blog_list = BlogsPost.objects.all()
    return render_to_response('index.html',{'blog_list':blog_list})
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

这里index函数做了两件事儿：

blog_list =BlogsPost.objects.all()

查询到BlogsPost数据库里的所有数据，赋值给blog_list变量。

return render_to_response('index.html',{'blog_list':blog_list})

通过render_to_response() 返回给浏览器一个index.html页面，并且将blog_list变量的值也返回给index.html。

 

 

# templates模板                                                                   

 

　　模板就是我们所熟悉的页面了，django自带的有模板系统。它的主要作用是如何展示数据，比如视图返回了一堆数据过来。是都循环显示出来呢？还通过判断只显示你认为有用的呢？ 当然，这里为了使页面更漂亮需要借助前端技术，比如css、JavaScript等。

　　然后，我们就在浏览器上看到了index.html页面了：

![img](https://images0.cnblogs.com/blog2015/311516/201503/272258326925946.jpg)

 

 

 

# MTV开发模式                               

 

了解了django的组成部分之间，我们再来深入的探讨一下django的开发模式。

　　**MTV 开发模式**

　　在钻研更多代码之前，让我们先花点时间考虑下 Django 数据驱动 Web 应用的总体设计。 我们在前面章节提到过，Django 的设计鼓励松耦合及对应用程序中不同部分的严格分割。 遵循这个理念的话，要想修改应用的某部分而不影响其它部分就比较容易了。 在视图函数中，我们已经讨论了通过模板系统把业务逻辑和表现逻辑分隔开的重要性。 在数据库层中，我们对数据访问逻辑也应用了同样的理念。 把数据存取逻辑、业务逻辑和表现逻辑组合在一起的概念有时被称为软件架构的 Model-View-Controller(MVC)模式。 在这个模式中， Model 代表数据存取层，View 代表的是系统中选择显示什么和怎么显示的部分，Controller 指的是系统中根据用户输入并视需要访问模型，以决定使用哪个视图的那部分。

　　为什么用缩写？

　　像 MVC 这样的明确定义模式的主要用于改善开发人员之间的沟通。 比起告诉同事，“让我们采用抽象的数据存取方式，然后单独划分一层来显示数据，并且在中间加上一个控制它的层”，一个通用的说法会让你收益，你只需要说：“我们在这里使用MVC模式吧。”。 Django 紧紧地遵循这种 MVC 模式，可以称得上是一种 MVC 框架。 以下是 Django 中 M、V 和 C 各自的含义：

-   M ，数据存取部分，由django数据库层处理，本章要讲述的内容。 
-   V ，选择显示哪些数据要显示以及怎样显示的部分，由视图和模板处理。
-   C ，根据用户输入委派视图的部分，由 Django 框架根据 URLconf 设置，对给定 URL 调用适当的 Python 函数。

 

　　由于 C 由框架自行处理，而 Django 里更关注的是模型（Model）、模板(Template)和视图（Views）， Django 也被称为 MTV 框架 。在 MTV 开发模式中：

-  M 代表模型（Model），即数据存取层。 该层处理与数据相关的所有事务： 如何存取、如何验证有效
-  T 代表模板(Template)，即表现层。 该层处理与表现相关的决定： 如何在页面或其他类型文档中进行显示。
-  V 代表视图（View），即业务逻辑层。 该层包含存取模型及调取恰当模板的相关逻辑。 你可以把它看作模型与模板之间的桥梁。

　　如果你熟悉其它的 MVC Web开发框架，比方说 Ruby on Rails，你可能会认为 Django 视图是控制器，而Django 模板是视图。 很不幸，这是对 MVC 不同诠释所引起的错误认识。 在 Django 对 MVC 的诠释中，视图用来描述要展现给用户的数据；不是数据 如何展现 ,而且展现 哪些 数据。 相比之下，Ruby on Rails 及一些同类框架提倡控制器负责决定向用户展现哪些数据，而视图则仅决定 如何展现数据，而不是展现 哪些 数据。

　　两种诠释中没有哪个更加正确一些。 重要的是要理解底层概念。