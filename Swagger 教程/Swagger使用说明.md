# Swagger使用说明

> 自动生成接口文档，保持代码和文档同步，集成在线测试



![img](https:////upload-images.jianshu.io/upload_images/311585-e61d8eb579991673.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)





![img](https:////upload-images.jianshu.io/upload_images/311585-08e9ab3cc6871e21.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)





![img](https:////upload-images.jianshu.io/upload_images/311585-cb1907cb6f54e9a2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)





![img](https:////upload-images.jianshu.io/upload_images/311585-b3943a11f5c158c1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



#### 相关注解解读

**@Api**
 用在类上，说明该类的作用

```
@Api(description = "用户相关api")
```

**@ApiOperation**
 用在方法上，说明方法的作用

```
@ApiOperation(value="创建用户")
```

**@ApiImplicitParams**
 用在方法上包含一组参数说明

多个参数

```
@ApiImplicitParams({
        @ApiImplicitParam(name = "id", value = "用户ID", required = true, dataType = "Long",paramType = "path"),
        @ApiImplicitParam(name = "user", value = "用户详细实体user", required = true, dataType = "User")
})
```

**@ApiImplicitParam**
 指定一个请求参数的各个方面

```
@ApiImplicitParam(name = "id", value = "用户ID", required = true, dataType = "Long", paramType = "path")
```

| 属性         | 取值   | 作用                                          |
| ------------ | ------ | --------------------------------------------- |
| paramType    |        | 查询参数类型                                  |
|              | path   | 以地址的形式提交数据                          |
|              | query  | 直接跟参数完成自动映射赋值                    |
|              | body   | 以流的形式提交 仅支持POST                     |
|              | header | 参数在request headers 里边提交                |
|              | form   | 以form表单的形式提交 仅支持POST               |
| dataType     |        | 参数的数据类型 只作为标志说明，并没有实际验证 |
|              | Long   |                                               |
|              | String |                                               |
|              | Object | 类名，会生成类的JSON结构                      |
| name         |        | 接收参数名                                    |
| value        |        | 接收参数的意义描述                            |
| required     |        | 参数是否必填                                  |
|              | true   | 必填                                          |
|              | false  | 非必填                                        |
| defaultValue |        | 默认值                                        |

用于表示一组响应

**@ApiResponse**
 用在@ApiResponses中，一般用于表达一个错误的响应信息
 code：数字，例如400
 message：信息，例如”请求参数没填好”
 response：抛出异常的类（可选）

```
 @ApiResponses(value = {
            @ApiResponse(code = 123, message = "No Name Provided")
    })
```



![img](https:////upload-images.jianshu.io/upload_images/311585-8af6b059ff207447.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/990/format/webp)

-w500



![img](https:////upload-images.jianshu.io/upload_images/311585-2ccdcb9bf0fa8ea5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/454/format/webp)

-w200



-w200

小礼物走一走，来简书关注我

作者：ZainYan

链接：https://www.jianshu.com/p/7871c716f38c

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。