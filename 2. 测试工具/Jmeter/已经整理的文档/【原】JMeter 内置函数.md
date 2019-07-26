

BeanShell Assertion

只读函数列表(ReadOnly)

| 函数名称        | 作用               | 用法                                                     | 返回类型 |
| --------------- | :----------------- | -------------------------------------------------------- | -------- |
| ResponseData    | 获取 response body | 因为返回类型问题，建议进行转换：new String(ResponseData) | byte[]   |
| ResponseCode    |                    |                                                          | String   |
| ResponseMessage |                    |                                                          | String   |
| ResponseHeaders |                    |                                                          | String   |
| Response        |                    |                                                          | Object   |
|                 |                    |                                                          |          |
|                 |                    |                                                          |          |
|                 |                    |                                                          |          |
|                 |                    |                                                          |          |



可读可写函数列表(Read/Write)

| 函数名称 | 作用 | 用法 | 返回类型 |
| -------- | ---- | ---- | -------- |
| Failure  |      |      |          |
|          |      |      |          |
|          |      |      |          |
|          |      |      |          |
|          |      |      |          |
|          |      |      |          |
|          |      |      |          |
|          |      |      |          |
|          |      |      |          |
|          |      |      |          |
|          |      |      |          |



```
//1.log 打印日志 log.info(“在控制台打印日志”);  
//2.SampleResult ，可以通过这个对象获取想要的信息  
//3.Response 获取Response对象，可以通过这个对象获取对应的信息  
//4.Failure 查看接口调用是否成功，如果返回false是成功的，true是失败的  
//5.FailureMessage 失败信息，没有设置的时候失败信息是空的。可以set这个信息  
//6.ResponseData 获取 response body，类型是byte[]  
//7.ResponseCode 返回接口code，成功是200  
//8.ResponseMessage 获取msg，成功是OK  
//9.ResponseHeaders 获取接口服务端返回的头部信息  
//10.RequestHeaders 获取客户端请求的头部信息  
//11.SampleLabel 获取接口请求的名称  
//12.SamplerData 获取请求的url和body  
//13.ctx 代表上下文信息，可以直接使用  
//14.vars 可以直接调用，将获取的数据变成jmeter变量(put)，也可以获取用户自定义的变量(get)  
//vars 只能在当前线程内使用，props 可以跨线程组使用
//vars 只能保存 String 或者 Object，props 是 Hashtable 对象

//SampleResult 对象类型
//
//Response 对象类型
//
//ResponseData byte[]
//
//ResponseCode 字符串
//
//ResponseMessage 字符串
//
//ResponseHeaders 字符串
//
//RequestHeaders 字符串
//
//SampleLabel 字符串
//
//SamplerData 字符串
//
//Successful  boolean

//log对象，可以利用此对象写日志
//　　　　SampleResult对象，从中获取响应数据、响应码等信息，只读
//　　　　Respose对象，获取响应数据，只读
//　　　　Failure，用来设置断言成功与否，Boolean类型
//　　　　FailureMessage，用来设置失败信息
//　　　　ResponseData对象，湖区响应数据
//　　　　ResponseCode，响应码
//　　　　ResponseMessage，响应信息
//　　　　ResponseHeaders，响应投信息
//　　　　RequestHeaders，请求头信息
//　　　　SampleLabel，取样器Label信息
//　　　　SamplerData，发送给服务器的数据
//　　　　Ctx （JMeterContext），JMeter上下文信息，从中可以获取线程数、线程号等信息
//　　　　Vars（JMeterVariables），获取JMeter中定义的变量，或者设置变量
//　　　（JMeterPoperties），获取JMeter中的属性，或者设置属性
//　　其他几个方法：
//　　　　getResponseDataAsString：从SampleResult获取响应数据
//　　　　getResponseCode：从SampleResult获取响应消息
//　　　　getResponseMessage：从SampleResult获取响应消息

log.info("----------------------------------------内置ReadOnly函数展示----------------------------------------");
log.info("++++++++++++++++++++++++++++++++++++++ ReadOnly: Response[Data|Code|Message|Headers], RequestHeaders, SampleLabel, SamplerData, ctx");
log.info("++++++++++++++++++++++++++++++++++++++++ 原生的ResponseData = "+ResponseData);
log.info("++++++++++++++++++++++++++++++++++++++++ 转换后的ResponseData = "+new String(ResponseData));
String reponseDataAsString = Response.getResponseDataAsString();
log.info("++++++++++++++++++++++++++++++++++++++++ reponseDataAsString = "+reponseDataAsString);
log.info("++++++++++++++++++++++++++++++++++++++++ ResponseCode = "+ResponseCode);
log.info("++++++++++++++++++++++++++++++++++++++++ ResponseMessage = "+ResponseMessage);
log.info("++++++++++++++++++++++++++++++++++++++++ ResponseHeaders = "+ResponseHeaders);
log.info("++++++++++++++++++++++++++++++++++++++++ RequestHeaders = "+RequestHeaders);
log.info("++++++++++++++++++++++++++++++++++++++++ SampleLabel = "+SampleLabel);
log.info("++++++++++++++++++++++++++++++++++++++++ SamplerData = "+SamplerData);
log.info("++++++++++++++++++++++++++++++++++++++++ ctx = "+ctx);
log.info("---------------------------------------------------------------------------------------------------");
log.info("---------------------------------------内置Read/Write函数展示---------------------------------------");
log.info("++++++++++++++++++++++++++++++++++++++++ isFailure = "+Failure);
log.info("++++++++++++++++++++++++++++++++++++++++ isSuccessful = "+Successful);
log.info("++++++++++++++++++++++++++++++++++++++++ FailureMessage = "+FailureMessage);
log.info("++++++++++++++++++++++++++++++++++++++++ SampleResult = "+SampleResult);
String reponseDataAsString2 = SampleResult.getResponseDataAsString();
log.info("++++++++++++++++++++++++++++++++++++++++ reponseDataAsString2 = "+reponseDataAsString2);
vars.put("str","123");
vars.put("obj",SampleResult);
log.info("++++++++++++++++++++++++++++++++++++++++ str = "+var.get("str"));
props.put("abc","456");
log.info("++++++++++++++++++++++++++++++++++++++++ str = "+props.get("abc"));
log.info("---------------------------------------------------------------------------------------------------");
```

