# Jmeter（四）——Config Element配置元件

2018年07月16日 17:47:19 [使劲追赶](https://me.csdn.net/qingdiao) 阅读数 448



1. **FTP Request Defaults（FTP请求默认值）**
   ![img](https://img-blog.csdn.net/20180716170851423?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FpbmdkaWFv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
   Server Name or Ip：服务器名称或者ip地址
   Port Number：服务器端口
   Remote File：远程ftp文件路径
   Local File：本地文件路径
   get（RETR）：下载文件
   put（STOR）：上传文件
   Use Binary mode ？：是否以二进制方式传输
   Save File in Response ？：文件内容是否保存到响应中，如果选择且请求运行成功，在结果响应数据中可以看到
2. **HTTP Authorization Manager**
   该属性管理器用于设置自动对一些需要**NTLM**验证的页面进行认证和登录
3. HTTP Request Defaults
   http请求较多且参数一致时，可以设置此项，就不用每次都再填一次参数了
   例如：设置了HTTP Request Defaults，请求访问百度，线程组下的http请求不用填写参数，执行请求百度成功
   ![img](https://img-blog.csdn.net/20180716172243632?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FpbmdkaWFv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
4. Random Variable
   ![img](https://img-blog.csdn.net/20180716173733113?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FpbmdkaWFv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
   Variable Name：参数名称
   Ouput Format：输出格式
   此项生成的随机变量会有重复值
5. **Counter**
   ![img](https://img-blog.csdn.net/2018071617414835?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FpbmdkaWFv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
   Starting value：开始数值
   Increment：步长
   Refence Name：变量名称
   Track counter independentiy foreach user：每个线程组都有自己的计数器，互不干扰
   Reset counter on each Thread Group Iteration：每次迭代复原计数器
   计数器生成的数值不会重复
6. Login Config Element
   ![img](https://img-blog.csdn.net/2018071617462816?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FpbmdkaWFv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
