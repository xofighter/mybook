# swagger完整教程。杜绝copy

这几天要给已接近上线的项目api接入swagger。之前都是用word文档。所以根据网上好评数选了swagger，但是配置过程中总有大大小小的bug，而百度出来的结果简直让人抓狂。一堆的copy的配置教程。copy就算了，还不给来源链接，有些很多有很多的错误。不能忍。希望大家如果看到好的文章或者有帮助的，收藏就行了。没必要拷贝到自己的博客。纯碎是为了塞满自己的博客看起来好看而已吗。。。

下面是教程：

有很感谢有先驱们的帮助，有些写了很有价值的解决方案，希望可以帮到正在摸索的人，我也是第一次用。

maven添加如下包：

```maven
<!-- swagger-springmvc -->
​    <dependency>
​        <groupId>com.mangofactory</groupId>
​        <artifactId>swagger-springmvc</artifactId>
​        <version>1.0.2</version>
​    </dependency>
​    <dependency>
​        <groupId>com.mangofactory</groupId>
​        <artifactId>swagger-models</artifactId>
​        <version>1.0.2</version>
​    </dependency>
​    <dependency>
​        <groupId>com.wordnik</groupId>
​        <artifactId>swagger-annotations</artifactId>
​        <version>1.3.11</version>
​    </dependency>
​    <!-- swagger-springmvc dependencies -->
​    <dependency>
​        <groupId>com.google.guava</groupId>
​        <artifactId>guava</artifactId>
​        <version>15.0</version>
​    </dependency>
​    <dependency>
​        <groupId>com.fasterxml.jackson.core</groupId>
​        <artifactId>jackson-annotations</artifactId>
​        <version>2.4.4</version>
​    </dependency>
​    <dependency>
​        <groupId>com.fasterxml.jackson.core</groupId>
​        <artifactId>jackson-databind</artifactId>
​        <version>2.4.4</version>
​    </dependency>
​    <dependency>
​        <groupId>com.fasterxml.jackson.core</groupId>
​        <artifactId>jackson-core</artifactId>
​        <version>2.4.4</version>
​    </dependency>
​    <dependency>
​        <groupId>com.fasterxml</groupId>
​        <artifactId>classmate</artifactId>
​        <version>1.1.0</version>
​    </dependency>
```
如上的建议参考这位作者：http://javatech.wang/index.php/archives/74/  一个很好的部署教程。

接下来是配置类
```java
@Configuration
@EnableSwagger
@EnableWebMvc
public class SwaggerConfig {

    private SpringSwaggerConfig springSwaggerConfig;
     
    /**
     * Required to autowire SpringSwaggerConfig
     */
    @Autowired
    public void setSpringSwaggerConfig(SpringSwaggerConfig springSwaggerConfig)
    {
        this.springSwaggerConfig = springSwaggerConfig;
    }
     
    /**
     * Every SwaggerSpringMvcPlugin bean is picked up by the swagger-mvc
     * framework - allowing for multiple swagger groups i.e. same code base
     * multiple swagger resource listings.
     */
    @Bean
    public SwaggerSpringMvcPlugin customImplementation()
    {
        return new SwaggerSpringMvcPlugin(this.springSwaggerConfig)
                .apiInfo(apiInfo())
                .includePatterns(".*api*.*");
    }
    
    private ApiInfo apiInfo()
    {
        ApiInfo apiInfo = new ApiInfo(
                "吃瓜app",
                "接口文档",
                "",
                "ywd979@foxmail.com",
                "",
                "");
        return apiInfo;
    }
}
```
说明：
 .includePatterns(".*api*.*");在此处，代表扫描的controller或者接口的名。有些教程在类开始处注解@compentScan，这个是无效的。

配置完成后在sping-mvc.xml配置一下：
```xml
<!-- 接口自动化文档 -->
​	<bean class="com.pricl.frame.swagger.SwaggerConfig" />
​	<bean class="com.mangofactory.swagger.configuration.SpringSwaggerConfig" />
```

配置到这里，访问ip:端口/api-docs，就能出来一堆json数据了，这些就是能够用swaggerUI来构成可视化可操作的界面的数据

至于注解，放到后面说。

如果你能访问刚才的地址能访问到json数据了，那么就可以开始配置swaggerUI了。
我用的是swaggerUI 2.5的版本。有bug，时间问题么有去找问题。（还是说我才有这个问题。）
去github下载release版本2.5.然后复制里面的dist文件夹的内容到你的项目任何能访问到的。
然后修改index.html的这个url参数为你刚才的地址
```
 if (url && url.length > 1) {
​        url = decodeURIComponent(url[1]);
​      } else {
​        url = "/api-docs";
​      }
```
然后直接访问该html，就能看到成果了

![img](https://img-blog.csdn.net/20170720164224444?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzI0NDY3NzU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

至此配置已经完成了。

接下来的不能满足的地方有如下几个：
1. 如果接口参数含有对象，然后swaggerUI提供的填入框是一个json格式的。提交后参数对象数据接收不到。
2. 给参数接口加@ApiParam注释后，swaggerUI的对应的参数框对象类型变成了body，数据同样接收不到了。
3. ui有bug，点击对应的接口详情不展开，只能点击右上的显示按钮才显示。（不知道是不是环境原因）。

先说第一个问题：
由于项目已经完成了大部分，接口对象接受用的是对象，所以swaggerUI提供的输入框json格式将没办法生效。

如果在对应的接口参数加入@RequestBody，spring会帮你转成对象属性注入到参数。但是这样项目就存在两种数据传输格式，后期人员维护将很不方便。

如图：

![img](http://images2015.cnblogs.com/blog/1009950/201608/1009950-20160818185738062-1701409693.png)

所以只能修改一下他们的参数type。

在此感谢这位作者：http://www.cnblogs.com/xmplatform/p/5785065.html

解决方案如下：

在接口描述的地方修改为

那么久回到我们熟悉的controller接受参数的模式。修改后页面显示为：

然后我们填写参数的时候就必须：x=1&y=2。。。这样的写法就行了。这样参数就能正常接收

第二个问题：只需要在对应的参数上添加：@RequestParam(requeired=false)，推荐为false，这样就算不传参也能处理返回一个json数据告知调用者

第三个问题：修改index.html.也有其他更好的，但是这个可能更快捷。
```java
 onComplete: function(swaggerApi, swaggerUi){
​          if(typeof initOAuth == "function") {
​            initOAuth({
​              clientId: "your-client-id",
​              clientSecret: "your-client-secret-if-required",
​              realm: "your-realms",
​              appName: "your-app-name",
​              scopeSeparator: " ",
​              additionalQueryStringParams: {}
​            });
​          }

          if(window.SwaggerTranslator) {
            window.SwaggerTranslator.translate();
          }
       	  // 修复不明bug，点击接口后不展开
          $(".toggleOperation").click(function() {
        		$(this).parent().parent().parent().next().css("display","block");
          })
        }
```
好了。

---------------------
作者：杨文东 
来源：CSDN 
原文：https://blog.csdn.net/qq_32446775/article/details/75546553 
版权声明：本文为博主原创文章，转载请附上博文链接！