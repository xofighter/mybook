API 文档是前后端对接的基本，但如果还停留在手写文档的阶段，那就真的太 out 了。大家可能也尝试过各种 API 接口管理的工具，比如 postman 、apizza 等，但个人使用下来还是感觉麻烦了，长期来看我是拒绝的。



![img](https:////upload-images.jianshu.io/upload_images/5378831-001dcd0b77401c08.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/300/format/webp)

reject

从目前 API 文档生成及管理上来看，Swagger 可算是不错的框架。今天来介绍一下 Swagger 的使用，以下使用 .NETCore Web API 为例，其他语言也类似，最终都是依赖生成的 json/yaml 文件。

### 文档生成

#### 内嵌代码方式

1. 新建 ASP.NET Core Web 应用程序，选择 API 类型

2. Nuget 安装 Swashbuckle.AspNetCore

   ```
   Install-Package Swashbuckle.AspNetCore
   ```

3. 在 Startup.cs 中的 ConfigureServices 和 Configure 方法添加 Swagger  相关代码

   ```
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
       
       // swagger.json 文档生成参数配置
       services.AddSwaggerGen(options =>
       {
           options.SwaggerDoc("v1", new Info
           {
               Title = "SwaggerTest接口文档",
               Version = "1.0.0"
           });
           
           options.IncludeXmlComments(Path.Combine(AppContext.BaseDirectory, "SwaggerTest.XML"));
       });
   }
   ```

   ```
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
       }
   
       app.UseMvc();
   
       app.UseSwagger();
       
       // 通过SwaggerUI展示
       app.UseSwaggerUI(options =>
       {
           options.SwaggerEndpoint("/swagger/v1/swagger.json", "SwaggerTest");
       });
   }
   ```

4. 项目属性中的 ”生成“ 勾选 “XML 文档文件”  （*Debug 和 Release 两种模式下都勾选上*）。这一步的目的是将代码注释生成文档，所以接口方法及参数都务必加上完整的注释
    



   ![img](https:////upload-images.jianshu.io/upload_images/5378831-c13c1cdcaab94c14.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/589/format/webp)

   build xml

5. 运行项目，访问 <http://localhost:62654/swagger/index.html>
    



   ![img](https:////upload-images.jianshu.io/upload_images/5378831-f91c368d183bf920.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/994/format/webp)

   swagger ui by code


以上方式使用起来非常简单，但个人觉得存在以下问题：

1. Startup 中需要加入 Swagger 相关的一些代码，不够整洁
2. 如果有多个 API 服务，每个服务都需要添加类似代码，而且每个服务的文档地址独立，不能统一管理

#### 非内嵌代码方式

[Swagger UI](https://github.com/swagger-api/swagger-ui) 本身是一个可以单独使用的前端项目。从 UseSwaggerUI 的参数配置来看：

```
app.UseSwaggerUI(options =>
{
    options.SwaggerEndpoint("/swagger/v1/swagger.json", "SwaggerTest");
});
```

其实只要将 "/swagger/v1/swagger.json"  这个文件引用进去即可，所以只需存在一种可以根据项目的 dll 文件生成 swagger.json 的方式，然后把生成的 swagger.json 引用到 Swagger UI 。[NSwag](https://github.com/RSuter/NSwag) 恰好可以满足这个需求。

抛开内嵌代码的方式，假设现在是一个全新的 Web API 项目。

1. 下载  [NSwag](https://github.com/RSuter/NSwag) ，在 Downloads 中下载 NSwag command line tools。如下载后解压地址为： C:\Users\Administrator\Downloads\NSwag\

2. 在项目根目录下新建 bat 文件并添加脚本，执行生成 swagger.json （*windows 环境*）

   ```
   :: 生成的文档名，每个项目最好是唯一的
   SET FILE_NAME=swaggerTest
   :: 项目dll地址
   SET SOURCE_DLL=bin\Release\netcoreapp2.1\publish\SwaggerTest.dll
   :: 文档生成的目标文件夹
   SET TARGET_DIR=D:\ApiDoc\public\docs\
   
   :: Swagger文档配置
   
   :: 标题
   SET TITLE=SwaggerTest接口文档
   :: 描述
   SET DESCRIPTION=SwaggerTest接口文档描述
   :: 版本
   SET VERSION=1.0.0
   :: 接口测试请求的host地址，不同的api服务有不同的host
   SET HOST=localhost:5000
   
   :: 通过 dotnet-nswag 执行生成命令
   dotnet C:\Users\Administrator\Downloads\NSwag\NetCore21\dotnet-nswag.dll webapi2swagger /assembly:"%SOURCE_DLL%" /AspNetCore:true /output:%TARGET_DIR%%FILE_NAME%.json /InfoTitle:"%TITLE%" /InfoDescription:"%DESCRIPTION%" /InfoVersion:%VERSION% /ServiceHost:%HOST% 
   ```

bat 脚本执行完成后，在 D:\ApiDoc\public\docs\ 目录下就会多出一个 swaggerTest.json，以这种方式我们并不需要修改任何代码。到目前为止，API 文档对应的 json 文件就有了，接下来就是把它通过 Swagger UI 展示出来。假设每个 API 项目都生成一个 json 文件到指定的目录，而 Swagger UI 本身支持配置多个文档 url 地址，这就意味着可以搭建出了一个 API 文档管理平台。



![img](https:////upload-images.jianshu.io/upload_images/5378831-12917ff6bdeafc52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/713/format/webp)

API 文档管理平台

### 文档管理

1. 下载 [Swagger UI](https://github.com/swagger-api/swagger-ui) 并解压

2. 搭建一个 Node.js 项目，可使用 Express 或者 Koa 框架（其他语言的项目也可以，任意选择）

   1. 将 swagger-ui dist 文件夹下的文件全部复制到项目的 public 文件夹下

   2. 安装 express

   3. 新建 index.js，添加如下代码

      ```
      const express = require('express');
      const app = express();
      
      app.use('/', express.static('public'));
      
      app.listen(3000, function () {
          console.log('app listening on http://localhost:3000');
      });
      ```

   4. 启动服务

      ```
      node index.js
      ```

完成以上步骤，Swagger UI 的站点就搭建完成了，访问 <http://localhost:3000> 会出现一个默认的 API 接口文档页面。

打开 public/index.html 会发现有这么一段代码：

```
const ui = SwaggerUIBundle({
    url: "https://petstore.swagger.io/v2/swagger.json",
    ...
})
```

这里的 url 配置了一个默认的 swagger.json文件的地址，所以我们只需要把 url 替换成上面生成的 swaggerTest.json 地址就可以了，将 swaggerTest.json 复制到 public/docs 目录下



![img](https:////upload-images.jianshu.io/upload_images/5378831-46c8c639c21ad59b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/196/format/webp)

swagger file

```
const ui = SwaggerUIBundle({
    url: "./docs/swaggerTest.json",
    ...
})
```



![img](https:////upload-images.jianshu.io/upload_images/5378831-02276441c20b8ddc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/796/format/webp)

wagger ui by tool

如果有多个 json 文件就不能使用 url 参数，需要修改成 urls 参数，具体可参考 [Swagger UI 参数配置](https://github.com/swagger-api/swagger-ui/blob/master/docs/usage/configuration.md)，为了不需要每次手动调整 urls，所以需要动态读取 docs 文件夹下的文件，生成的文档只需要传到这个目录下就可以直接查看和测试。

在 index.js 增加一个接口：

```
app.get('/getDocs', function (req, res) {
    let docFileInfos = [];
    let pathDir = __dirname + '/public/docs';
    try {
        let fileNames = fs.readdirSync(pathDir);
        fileNames.forEach(function (fileName) {
            let data = fs.readFileSync(pathDir + '/' + fileName, 'utf8');
            data = data.trim();
            let json = JSON.parse(data);
            docFileInfos.push({
                url: './docs/' + fileName,
                name: json.info.title
            });
        });
    } catch (err) {
        console.log(err);
    }
    res.send(docFileInfos);
});
```

public/index.html 引入 axios，并调用 getDocs 接口获取结果，将 response.data 赋给 SwaggerUIBundle 的 urls 参数。

```
axios.get('/getDocs')
    .then(function (response) {
        if (response.status === 200 && response.data.length) {
            ...
        }
    });
```

当存在多个 json 文件的时候，可以从下拉选项中进去切换



![img](https:////upload-images.jianshu.io/upload_images/5378831-f17f39cbd66efe7b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/579/format/webp)

multi swagger file

### 参考链接

- [NSwag](https://github.com/RSuter/NSwag)
- [Swagger UI](https://github.com/swagger-api/swagger-ui)
- [SwaggerSample](https://github.com/beckjin/SwaggerSample)
- [APIDocSample](https://github.com/beckjin/APIDoc)

作者：BeckJin

链接：https://www.jianshu.com/p/a7a7aa189522

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。