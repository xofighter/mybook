Swagger(http://swagger.io/docs/)主要提供了三个功能:

Swagger Editor: Swagger提供的一个编辑器，用来通过Swagger提供的特定的YAML语法来编写API文档
Swagger Codegen: 代码生成器
Swagger UI: YAML语法定义我们的RESTful API，然后它会自动生成一篇排版优美的API文档，并且提供实时预览。
我们只需要安装Swagger Editor就足够了，它基本上集成了其余两个工具 
Swagger Editor通过远程服务器(http://editor.swagger.io)也可以使用,但速度较慢，我们在本机安装。

Swagger Editor的安装:
首先确保你的电脑安装了NodeJs环境。

```shell
npm install -g http-server
wget https://github.com/swagger-api/swagger-editor/releases/download/v2.10.4/swagger-editor.zip
unzip swagger-editor.zip
http-server swagger-editor
```

运行成功后通过本机IP即可使用Swagger Editor,比通过Swagger服务器快的多。

在Swagger Editor中定义API
通过Swagger提供的特定的YAML语法来定义API,可以详细的描述API的方法,功能，参数，返回值等信息。现在通过一个简单的示例，学习编写Swagger的YAML文档以及生成服务端代码。

通过Swagger定义登陆，注册，修改，删除四个RestFul接口:

> - 查询所有的User => /users					使用get方法
> - 新增User => /users						使用post方法
> - 根据某个id查询对应的User => /users/{id}		{id}指定了你要查询的那个id,使用get方法
> - 根据某个id删除对应的User => /users/{id}		{id}指定了要删除的那个id,使用delete方法
> - 修改某个id对应的User => /users/{id} 　　　　　{id}指定了要修改的那个id,使用patch方法 

一个完整的，可阅读的Swagger YAML的格式层次关系定义如下:

```yaml
swagger:
info:
  title:
  description:
  version:
host: 
basePath:
schemes:
produces:
paths:
  /users:
    get:
      summary:
      description:
      tags:
      responses:
        httpResponseCode:
          description:
          schema:
        ...#与httpResponseCode同级，可描述多个状态吗
    post: 
      summary:
      description:
      tags:
      responses:
        httpResponseCode:
          description:
          schema:
        ...#与httpResponseCode同级，可描述多个状态吗

 　　...#与httpMethod同级，对于同一个URL可定义多个方法，表示不同的接口
  /users/{id}:
    delete:
      summary:
      description:
      tags:
      responses:
        httpResponseCode:
          description:
          schema:
        ...#与httpResponseCode同级，可描述多个状态吗
    patch:
      summary:
      description:
      tags:
      responses:
        httpResponseCode:
          description:
          schema:
        ...#与httpResponseCode同级，可描述多个状态吗
```

假设数据库中存在一个User表,表字段为_id,username,password,alias。对该表定义出CURD操作接口
通过Swagger对于上述的接口编写文档，示例如下:

```yaml
#必要字段！Swagger规范版本，必须填2.0，否则该YAML将不能用于Swagger其他组件
swagger: '2.0'
#必要字段！描述API接口信息的元数据
info:
  #接口标题
  title: swagger说明文档　
  #接口文档的描述
  description: 学习Swagger
  #版本号
  version: 1.0.0
#Swagger会提供测试用例，host指定测试时的主机名，如果没有指定就是当前主机,可以指定端口．
host: 127.0.0.1
#定义的api的前缀，必须已/开头,测试用例的主机则为:host＋bashPath
basePath: /api
#指定调用接口的协议，必须是:"http", "https", "ws", "wss"．默认是http.-表示是个数组元素，即schemes接受一个数组参数
schemes:

- http
- https
  #对应与http协议头request的Accept，调用者可接受类型,默认是*/*,定义的类型必须是http协议定义的 Mime Types,RestfulAPI一般定义成application/json
  #这两个是对所有接口的全局设置，在细化的接口中是还可以对应这两个属性来覆盖全局属性
  produces:
- application/json
  #必要字段!定义可有可操作的API
  paths:
    /users:
     #必要字段!定义HTTP操作方法，必须是http协议定义的方法
  get:
    #接口概要
    summary: 查询所有用户信息
    #接口描述
    description: 查询出所有用户的所有信息，用户名，别名
    #标签，方便快速过滤出User相关的接口
    tags:
  - User
    #返回值描述，必要自动
      responses:
        #返回的http状态码
        200:
          description: 所有用户信息或者用户的集合信息
          #描述返回值
          schema:
    #返回值格式，可选的有array,integer,string,boolean
    type: array
    #针对array,每个条目的格式,type定义为array．必要填写items
    items:
      #引用在definitions下定义的Users
      $ref: '#/definitions/User'
        #执行出错的处理
        default:
          description: 操作异常,执行失败.返回信息描述错误详情
          schema:
    #值类型
    type: object
    #定义属性
    properties:
    #属性名
      message:
        #类型
        type: string
    #即对于同一个url定义两个不同的方法，表示两个接口
    post:
      description: 注册一个用户
      #请求参数
      parameters:
          #参数key
    - name: username
      #传递方法，formData表示表单传输，还有query表示url拼接传输，path表示作为url的一部分
      #body表示http头承载参数(body只能有一个,有body不能在有其他的)
      in: formData
      #参数描述
      description: 用户名，不能使用已经被注册过的
      #参数是否必要，默认false
      required: true
      #参数类型，可选的包括array,integer,boolean,string.使用array必须使用items
      type: string
      - name: password
        in: formData
        description: 用户登陆密码，加密传输，加密存储
        required: true
        type: string
        - name: alias
          in: formData
          type: string
          description: 用户别名
          #非必要字段
          required: false
            responses:
              #返回的http状态码
              200:
          description: 通过返回值来标示执行结果　返回true表示执行成功
          schema:
           #值类型
            type: object
            #定义属性
            properties:
            #属性名
              status:
          #类型
          type: boolean
          #描述
          description: 是否成功
              #执行出错的处理
              default:
          description: 操作异常,执行失败.返回信息描述错误详情
          schema:
          #值类型
          type: object
          #定义属性
          properties:
          #属性名
            message:
              #类型
              type: string
            /users/{id}:
          #{id}表示id为请求参数，例如/users/1,/users/2都是对该API的请求，此时id即为１和2
          get:
            summary: 根据用户名id查询该用户的所有信息
            description: 查询出某个用户的所有信息，用户名，别名等
            tags:
          - User
            parameters:
                #上面接口中定义了{id}，则参数列表中必须包含参数id,并且请求类型为path
            - name: id
              in: path
              description: 要查询的用户的用户名,它是唯一标识
              required: true
              type: string
                responses:
                  200:
              description: 所有用户信息或者用户的集合信息
              schema:
                $ref: '#/definitions/User'
                  default:
              description: 操作异常,执行失败.返回信息描述错误详情
              schema:
                #值类型
                type: object
                #定义属性
                properties:
                #属性名
                  message:
              #类型
              type: string
              #http定义的delete方法,删除一个资源
              delete:
                summary: 删除用户
                description: 删除某个用户的信息，被删除的用户将无法登陆
                parameters:
              - name: id
                in: path
                type: string
                required: true
                description: 用户的唯一标示符
                  tags:
                - User
                  responses:
                      200:
                  description: 通过返回值来标示执行结果　返回true表示执行成功
                  schema:
                   #值类型
                    type: object
                    #定义属性
                    properties:
                    #属性名
                      status:
                  #类型
                  type: boolean
                  #描述
                  description: 是否成功
                      default:
                  description: 操作异常,执行失败.返回信息描述错误详情
                  schema:
                    #值类型
                    type: object
                    #定义属性
                    properties:
                    #属性名
                      message:
                  #类型
                  type: string
                  #描述错误信息
                  #http定义的patch方法，表示修改一个资源
                  patch:
                    summary: 用户信息修改
                    description: 修改用户信息(用户名别名)
                    parameters: 
                  - name: id
                    in: path
                    description: 用户名,要修改的数据的唯一标识符
                    required: true
                    type: string
                    - name: alias
                      in: formData
                      description: 新的用户别名
                      required: true
                      type: string
                        tags:
                      - User
                        responses:
                            200:
                        description: 通过返回值来标示执行结果　返回true表示执行成功
                        schema:
                        #值类型
                          type: object
                          #定义属性
                          properties:
                          #属性名
                            status:
                        #类型
                        type: boolean
                        #描述
                        description: 是否成功
                            default:
                        description: 操作异常,执行失败.返回信息描述错误详情
                        schema:
                          #值类型
                          type: object
                          #定义属性
                          properties:
                          #属性名
                            message:
                        #类型
                        type: string
                        #描述错误信息
                        definitions:
                          User:
                        #值类型
                        type: object
                        #定义属性
                        properties:
                        #属性名
                          id:
                            #类型
                            type: string
                            #描述
                            description: 用户的唯一id
                          username:
                            type: string
                            description: 用户名
                          alias:
                            type: string
                            description: 别名
```

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170214151300334?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2Fub3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170214151311271?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2Fub3Q=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

生成代码
　以Spring为例子: 

　 点击Swagger Editor上方的generate server，点击Spring。则会根据该API描述生成一套基于Maven构建的Spring-Boot项目，通过安装相关依赖，运行mvn spring-boot:run则成功运行项目，相关的接口均可进行测试。但这个mock server对于接口并没有返回值，如果需要一个完整的mock server还需要在此基础上使用其他工具集成。
　 
---------------------
作者：不能说的秘密go 
来源：CSDN 
原文：https://blog.csdn.net/canot/article/details/55051346 
版权声明：本文为博主原创文章，转载请附上博文链接！