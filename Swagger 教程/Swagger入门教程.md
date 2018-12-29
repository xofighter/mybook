# [译]5.41 Swagger tutorial

### [单击此处查看原文](http://idratherbewriting.com/pubapis_swagger/#about-swagger)

> 更多概念参见：[Implementing Swagger with your API docs](http://idratherbewriting.com/pubapis_swagger_intro/)

## 关于 Swagger

Swagger能成为最受欢迎的REST APIs文档生成工具之一，有以下几个原因：

- Swagger 可以生成一个具有互动性的API控制台，开发者可以用来快速学习和尝试API。
- Swagger 可以生成客户端SDK代码用于各种不同的平台上的实现。
- Swagger 文件可以在许多不同的平台上从代码注释中自动生成。
- Swagger 有一个强大的社区，里面有许多强悍的贡献者。

Swagger 文档提供了一个方法，使我们可以用指定的 JSON 或者 YAML 摘要来描述你的 API，包括了比如 names、order 等 API 信息。

你可以通过一个文本编辑器来编辑 Swagger 文件，或者你也可以从你的代码注释中自动生成。各种工具都可以使用 Swagger 文件来生成互动的 API 文档。

> 注意：用 Swagger 文件生成互动的 API 文档是最精简的，它展示了资源、参数、请求、响应。但是它不会提供你的API如何工作的其他任何一个细节。

## Petstore 的 Swagger 例子

为了更好的理解 Swagger ，我们现在来探索一下 Petsotre 项目的例子。记住：以下出现的 UI 都是指Swagger UI。Swagger 可以被展示成不同的视觉样式，这取决于你所使用到的视觉框架。

![Petstore UI](http://img.blog.csdn.net/20170208095154818?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXlKb2lU/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

有三个资源：pet，store，user。

### 创建一个 pet

1、展开 pet 的 post 方法
2、然后单击 Model Schema 中的黄色字体的 JSON。

![pet](http://img.blog.csdn.net/20170208095551477?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXlKb2lU/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这里用 JSON 填充了 body value。这里的 JSON 是你必须上传的，用于创建 pet 资源。

3、将第一个 id 标签的值修改一下（你必须保证 id 值都是唯一的，并且不能从 0 开始）。

4、将 name 标签的值修改一下（最好也要唯一，方便对比结果），以下是示例代码：

```
{
   "id": 37987,
   "category": {
     "id": 0,
     "name": "string"
   },
   "name": "Mr. Fluffernutter",
   "photoUrls": [
     "string"
   ],
   "tags": [
     {
       "id": 0,
       "name": "string"
     }
   ],
   "status": "available"
 }
```

5、单击 Try it out! 按钮，查看响应：

![这里写图片描述](http://img.blog.csdn.net/20170208100336944?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXlKb2lU/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 通过 ID 查询 pet

展开 Get 方法：pet/{petId}，输入你的 petId，单击 Try it out!，你创建的 pet 就会显示在 response 中。
默认情况下，api 响应都是 xml。可以把 Response Content Type 修改为 application/json 再试一次。
返回的值将会是 JSON 格式

> 注意：值得强调的是 Swagger 文档一定要通过着手尝试来学习。你要通过实实在在的发送请求和查看响应来更好的理解 Petstore API 是如何工作的。但是还要注意现在你已经在你的真实Petstore数据库中创建了一个新的pet。从学习尝试 API 过渡到在生产环境中使用 API 的时候，那些测试数据你可能都需要将它们删除。

## 整理 Swagger 组件

Swagger 分成一些不同的块。

Swagger spec：这一块对元素的嵌套、命令等采用官方模式。如果你想要对 Swagger 文件手动编码，你必须非常熟悉 Swagger spec。

Swagger editor：这是在线编辑器，用于验证你的 YML 格式的内容是否违反 Swagger spec 。YML 是一种句法，依赖于空格和嵌套。你需要对 YML 句法很熟悉才能很好的遵守 Swagger spec 规范。Swagger 编辑器会标出错误并且给你格式提醒（Swagger spec 文件可以使用 JSON 或者 YAML 中的任意一种格式）。

![这里写图片描述](http://img.blog.csdn.net/20170208102812743?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXlKb2lU/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

Swagger-UI：这是一套 HTML/CSS/JS 框架用于解析遵守 Swagger spec 的 JSON 或 YML 文件，并且生成API文档的UI导航。它可以将你的规格文档转换成Swagger Petsotre-like UI。

Swagger-codegen：这个工具可以为不同的平台生成客户端 SDK（比如 Java、JavaScript、Python 等）。这些客户端代码帮助开发者在一个规范平台中整合 API ，并且提供了更多健壮的实现，可能包含了多尺度、线程，和其他重要的代码。SDK 是用于支持开发者使用 REST API 的工具。

## 一些 Swagger 的实现例子

- [Reverb](https://reverb.com/swagger#!/accounts)
- [VocaDB](http://vocadb.net/swagger/ui/index)
- [Watson Developer Cloud](http://www.ibm.com/smarterplanet/us/en/ibmwatson/developercloud/apis/)

它们大多看起来一样。你会注意到 Swagger 实现的文档都很精简。这是因为 Swagger 的展示都是互动的体验，你可以尝试请求和查看响应，使用你自己的 API 密钥来查看你自己的数据。它是边看边做边学的形式。

它也有一些缺陷：

- 没有太多的空间来描述后端详细的工作
- 每个 Swagger UI 的输出看起来都差不多
- Swagger UI 会从你其他的文档中分离成独立的一个站点

## 创建 Swagger UI 展示

本节我们将为使用[Mashape Weather API](https://www.mashape.com/fyhao/weather-13#weatherdata)的 weatherdata 后台来创建一个 Swagger UI （weatherdata是之前的课程中创建的一个项目）。你可以在这里查看[demo](http://idratherbewriting.com/files/restapicourse/swagger)。

![这里写图片描述](http://img.blog.csdn.net/20170208112000190?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXlKb2lU/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### a.创建一个 Swagger spec 文件

1、去[Swagger online editor](http://editor.swagger.io/#/)
2、选择 File > Open Example 然后选择 PetStore Simple 。单击 Open。

你可以使用 weatherdata 后台文档来自定义这个示例 YML 文件。但如果你是新手， 它会带你认识spec。方便起见，只需要用到下面的文件，然后复制一份到 Swagger editor: swagger.yaml。

```
swagger: "2.0"
info:
 version: "1.0.0"
 title: "Weather API"
 description: "A sample API that uses a Mashape weather API as an example to demonstrate features in the swagger-2.0 specification"
 termsOfService: "http://helloreverb.com/terms/"
 contact:
   name: "Tom Johnson"
   email: "tomjohnson1492@gmail.com"
   url: "http://swagger.io"
 license:
   name: "MIT"
   url: "http://opensource.org/licenses/MIT"
host: "simple-weather.p.mashape.com"
schemes:
 - "https"
consumes:
 - "application/json"
produces:
 - "application/text"
paths:
 /aqi:
   get:
     tags:
       - "Air Quality"
     description: "gets air quality index"
     operationId: "getAqi"
     produces:
       - "text"
     parameters:
       -
         name: "lat"
         in: "query"
         description: "latitude"
         required: false
         type: "string"
       -
         name: "lng"
         in: "query"
         description: "longitude"
         required: false
         type: "string"
     responses:
       200:
         description: "aqi response"
       default:
         description: "unexpected error"

 /weather:
   get:
     tags:
       - "Weather Forecast"
     description: "gets weather forecast in short label"
     operationId: "getWeather"
     produces:
       - "text"
     parameters:
       -
         name: "lat"
         in: "query"
         description: "latitude"
         required: false
         type: "string"
       -
         name: "lng"
         in: "query"
         description: "longitude"
         required: false
         type: "string"
     responses:
       200:
         description: "weather response"
       default:
         description: "unexpected error"
 /weatherdata:
   get:
     tags:
       - "Full Weather Data"
     description: "Get weather forecast by Latitude and Longitude"
     operationId: "getWeatherData"
     produces:
       - "application/json"
     parameters:
       -
         name: "lat"
         in: "query"
         description: "latitude"
         required: false
         type: "string"
       -
         name: "lng"
         in: "query"
         description: "longitude"
         required: false
         type: "string"
     responses:
       200:
         description: "weatherdata response"
       default:
         description: "unexpected error"

securityDefinitions:
  internalApiKey:
    type: apiKey
    in: header
    name: X-Mashape-Key
```

> 注意：使用 YML 替换JSON。 YML 句法比 JSON 可读性高。使用 YML ，空格很重要。新段落需要缩进两个空格。冒号表示个对象。连字符代表一个 sequence 或者 list （像一个 array）。如果你下载这个文件而不是复制粘贴，你基本不会碰到空格问题。

Swagger editor 告诉你文件如何被输出，你也可以看到验证出来的错误。没有这个在线编辑器，你只能在运行代码的时候才能知道 YML 句法是否有效 （并且看到错误提示， YAML 文件也不能被正确解析）。

3、保证 Swagger edirot 中的 YAML 文件有效。如果有错误必须要修正。

4、选择 File > Download YAML 并且保存为 swagger.yaml 到本地（你可以只复制代码然后粘贴到空白文件中，命名为 swagger.yaml）。

你也可以选择 JSON 格式，不过 YAML 可读性更高。

### b.设置 Swagger UI

1、Github: [Swagger UI](https://github.com/swagger-api/swagger-ui)。下载、解压。只需要用到 dist 文件夹。除非你想重新生成 dist 中的文件，才会用到别的。

2、打开 dist > index.html

3、找到：`url = "http://petstore.swagger.io/v2/swagger.json";`

4、值改成 swagger.yaml 的路径

5、保存打开index.html

### c.上传文件到 web 主机

除了本地浏览 Swagger 文件，你也可以使用 **XAMPP** 在本地运行一个 web 服务器

1、下载安装[XAMPP](https://www.apachefriends.org/)

2、在你的应用程序文件夹中打开 XAMPP 文件夹，启动 manager-osx 控制台

3、单击 Manage Servers tab

4、选择 Apache Web Server 单击 Start

5、打开 XAMPP 中的 htdocs 文件夹。如果是Mac，通常在 /Applications/XAMPP/xamppfiles/htdocs。

6、将 dist 文件夹拖到此处

7、在你的浏览器中浏览 localhost/dist

就可以看到 Swagger UI的展示。

### 体验 Swagger UI

1、用浏览器浏览 Swagger UI。
2、在右上角，单击 Authorize 并且输入你的 Mashape API Key。如果你没有 Mashape API Key，你可以3、使用 `EF3g83pKnzmshgoksF83V6JB6QyTp1cGrrdjsnczTkkYgYrp8p`。
4、去 Google Maps 搜索一个地址。
5、从 URL 中去获取经纬度，将其插入到你的 Swagger UI中（比如：1.3319164 for lat, 103.7231246 for lng）
6、单击 Try it out。

如果成功，你应该可以看到 response body 的响应：`9 c, Mostly Cloudy at South West, Singapore`

如果你看到了`Not supported`，尝试调整经纬度。

如果你刷新了界面，你要重新输入 API Key。

## 从注释中自动生成 Swagger 文件

为了代替 Swagger file 的手动编码，你也可以从你的程序代码注释中自动生成。有许多的 Swagger 库可以用来整合不同的代码库。这些 Swagger 库可以解析注释并且生成跟之前手动生成相同的 Swagger 文件。

要整合 Swagger 到自己的代码中，要允许开发者便捷的撰写文档，保证新特性都会被记录，并且保持文档的更新。这里是一个教程，[使用 Swagger 为 Scalatra 从注释中自动生成文档](http://www.infoq.com/articles/swagger-scalatra).这些注释方法根据不同的语言分成不同的 Swagger 文档块。

其他工具参见 [Swagger services and tools](http://swagger.io/open-source-integrations/)