随着互联网技术的发展，现在的网站架构基本都由原来的后端渲染，变成了：前端渲染、先后端分离的形态，而且前端技术和后端技术在各自的道路上越走越远。 
前端和后端的唯一联系，变成了API接口；API文档变成了前后端开发人员联系的纽带，变得越来越重要，swagger就是一款让你更好的书写API文档的框架。

其他API文档工具
没有API文档工具之前，大家都是手写API文档的，在什么地方书写的都有，有在confluence上写的，有在对应的项目目录下readme.md上写的，每个公司都有每个公司的玩法，无所谓好坏。

书写API文档的工具有很多，但是能称之为“框架”的，估计也只有swagger了。 
在此先介绍一款其他的API文档工具，叫rap，这玩意儿用一句话就能概括：解放生产力，代替手写API的web工具。 
RAP写起来确实比手写文档要快，看看图就知道： 
可以选择某个项目，写针对某个项目的API 

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170827175130769?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaTY0NDgwMzg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

请看，可以填写请求和相应的字段 

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170827175556715?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaTY0NDgwMzg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

还可以选择字段对应的类型 

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170827175340567?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaTY0NDgwMzg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

类似的API文档工具网上还有很多，但是能拿上台面的，不多。RAP是由阿里开发的，整个阿里都在用，还不错。github地址为：https://github.com/thx/RAP 
当然咯，rap不可能只有线上版本，肯定可以部署到私服上。 
https://github.com/thx/RAP/wiki/deploy_manual_cn

swagger
rap挺好的，但是和swagger比起来有点轻量。 
先看看swagger的生态使用图：

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170827202033991?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaTY0NDgwMzg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


其中，红颜色的是swaggger官网方推荐的。

下面再细看看swagger的生态的具体内容：

swagger-ui
这玩意儿从名字就能看出来，用来显示API文档的。和rap不同的是，它不可以编辑。

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170827190923413?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaTY0NDgwMzg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

点击某个详细API的可以试。

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170827191115007?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaTY0NDgwMzg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

swagger-editor
就是一个在线编辑文档说明文件（swagger.json或swagger.yaml文件）的工具，以方便生态中的其他小工具（swagger-ui）等使用。 
左边编辑，右边立马就显示出编辑内容来。 

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170827201358685?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaTY0NDgwMzg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


编辑swagger说明文件使用的是yaml语法具体的内容可以去官网查看。

各种语言版本的根据annotation或者注释生成swagger说明文档的工具
目前最流行的做法，就是在代码注释中写上swagger相关的注释，然后，利用小工具生成swagger.json或者swagger.yaml文件。

目前官方没有推出。github上各种语言各种框架各种有，可以自己搜吧搜吧，这里只说一个php相关的。 
swagger-php :https://github.com/zircote/swagger-php

swagger-validator
这个小工具是用来校验生成的文档说明文件是否符合语法规定的。用法非常简单，只需url地址栏，根路径下加上一个参数url，参数内容是放swagger说明文件的地址。即可校验。 
例如： 

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170827203321054?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaTY0NDgwMzg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

docker hub地址为：https://hub.docker.com/r/swaggerapi/swagger-validator/ 
可以pull下镜像来自己玩玩。

swagger-codegen
代码生成器，脚手架。可以根据swagger.json或者swagger.yml文件生成指定的计算机语言指定框架的代码。 
有一定用处，Java系用的挺多。工业上应该不咋用。

mock server
这个目前还没有找到很合适的mock工具，包括rap也好，其他API文档工具也好，都做的不够完善，大多就是根据说明文件，例如swagger.json等生成一些死的静态的mock数据，不能够根据限定条件：例如“只能是数字，必传”等做出合理的回应。

---------------------
