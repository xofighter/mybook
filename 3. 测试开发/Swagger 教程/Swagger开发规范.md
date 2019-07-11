title: Swagger开发规范 [推荐]
 date: 2018-08-18 20:22:00
 categories:

- Spring Components
   tags:
- spring
- springfox-swagger

------

Springfox Swagger 和Spring的整合已经让我们可以动态的生成接口文档了，但是接口文档的定义需要遵循一定的规范，才能大大提高项目团队间的接口对接效率。

------

# Swagger开发规范 [推荐]

## swagger JSON文档生成

> maven-surefire-plugin(指定Swagger测试用例)

```
notes\env\maven\2018-06-27-maven-surefire-plugin.md
```

> 虚拟注入和扫描

```
notes\spring\components\2018-06-08-springfox-swagger-jenknis.md
```

## 接口定义规范

- 接口分组
- 接口组描述

> 鉴权方式描述

在description中描述该组接口的鉴权方式，可以使用超链接等方式也可以直接在其中说明

> 能力级描述

```
/** 在description中描述该组接口的能力集 */
 new ApiInfoBuilder().contact(contact).description("api描述1111").version("1.0").title("标题").build();
```

> 联系人信息

```
/** 在contact中描述联系人信息，接口得有负责人 */
new ApiInfoBuilder().contact(contact).description("api描述1111").version("1.0").title("标题").build();
```

- 配置类注解是否包含基本注解（Spring 注入）

```
@Configuration
@EnableSwagger2
public class SwaggerConfig {...}
```

### Controller控制器类(或在接口Interface中定义)

- 使用注解@api时是否对value description tags属性都进行了描述

```
    @Api(value = "API测试", description = "API接口描述，主要作为示例使用", tags = "测试1")
```

- 注解@api的description属性是否描述了该类接口的概括描述和能力集

```
    @Api(value = "API测试", description = "API接口描述，能力1，能力2...", tags = "测试1")
```

- 注解@apioperation是否对value、notes、tags属性进行描述

```
    @ApiOperation(value = "call_1接口", notes = "call_1接口描述", tags = "测试1")
```

- 注解@apioperation的属性notes是否描述了接口的详细情况和能力

> notes描述在pdf中的排版

```
"\t\tHead: " +
"\t\t\t\t PublicKey -> 公钥\n" +
"\t\t\t\t P -> P参数\n" +
"\t\t\t\t G -> G参数\n" +
"\t\t\t\t Token -> 双向认证令牌\n" +
```

- 多个简单参数情况下是否使用注解@ApiImplicitParams

```
    @ApiImplicitParams({
        @ApiImplicitParam( value = "姓名", name = "name", required = true, paramType = "query", dataType = "string"),
         @ApiImplicitParam( value = "年龄", name = "age", required = true, paramType = "query", dataType = "int"),
        @ApiImplicitParam( value = "日期", name = "birthday", required = true, paramType = "query", dataType = "date",    defaultValue = "2017-01-01")
 })
```

- 注解@ApiImplicitParam的属性value, name, required, paramType, dataType , defaultValue 等是否都有描述

```
    value = "日期", name = "birthday", required = true, paramType = "query", dataType = "date", defaultValue = "2017-01-01"
```

### 数据传输对象注解

- 返回数据对象基类是否使用了泛型定义

```
    @ApiModel(value = "返回数据对象", description = "描述返回数据对象")
    public class Result<T>{}
```

- 复杂出入参是否使用数据传输对象进行封装

```
@ApiModel(value = "返回数据对象", description = "描述返回数据对象")
public class Result<T> {

   @ApiModelProperty(required = true, value = "返回码", dataType = "int", example = "0", position = 0)
   protected int code;

   @ApiModelProperty(required = true, value = "返回信息", dataType = "string", example = "买买买", position = 1)
   protected String message;

   @ApiModelProperty(required = true, value = "其他返回信息", position = 2)
   protected T data;
}
```

- 数据传输对象类名上是否有注解@Apimodel

```
@ApiModel(value = "数据对象", description = "描述返回数据对象")
public Class Dto1{}
```

- 数据传输对象的属性注解@apimodelproperty是否对属性required, value, dataType, example, position都有描述(position字段在1.5.X后被废弃，可以不进行设置)

```
    required = true, value = "返回码", dataType = "int", example = "0", position = 0
```

- 数据对象属性需要自定义名称的是否使用了@JsonProperty注解

```
@ApiModelProperty(required = true, value = "测试自定义名称", dataType = "string", example = "AAACCDSF", position = 3)
@JsonProperty(value = "AAC")
private String AAC;
```

### 备注

> Swagger接口定义要求明确的出入参的必要性：

```
1、自动生成接口文档，可以直接生成返回数据体
2、前端开发依赖swagger.json生成的静态Mock Server，可直接模拟构建前端请求返回的数据体。
3、接口说明注解写得越详细，后期联调和前端自主开发都会大大节约时间，这也是前后端分离的初衷。
```

- 返回参数要求

1、ActionResult的返回数据体以**泛型**传入

```
return new ActionResult<Dto1>();
```

2、泛型支持类型 [ 前提是返回参数明确 ]集合对象包裹的Dto对象

```
List<Dto1>、List<Map<String,Dto1>>、Map<String,Dto1>、Dto1 { Dto2,Dto3,…}(对象嵌套)
```

3、不支持以Object形式、JsonObject形式返回参数

### 接口描述

> ApiModelProperty

- dateType

```
- 类名（引用路径）
/*如果该类不存在或是直接使用名称[无法通过Class.forName(...)生成]，原来的返回类型Original会被保留，*/

@ApiModelProperty(dataType = "com.qualified.ReplacedWith")
public Original getOriginal() { ... }
- baseTypes
     private static final Set<String> baseTypes = newHashSet(
      "int",
      "date",
      "string",
      "double",
      "float",
      "boolean",
      "byte",
      "object",
      "long",
      "date-time",
      "__file",
      "biginteger",
      "bigdecimal",
      "uuid");
```

## 

作者：离兮丶斜阳

链接：https://www.jianshu.com/p/fa4ec2ecd631

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。