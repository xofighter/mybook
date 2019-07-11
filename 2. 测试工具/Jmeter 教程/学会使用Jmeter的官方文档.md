学会使用Jmeter的API文档



> [Jmeter 官方文档](http://jmeter.apache.org/usermanual/get-started.html)
>
> [Jmeter 官方 API](http://jmeter.apache.org/api/)
>
> [Jmeter 官方API overview-summary](http://jmeter.apache.org/api/overview-summary.html)

在使用jmeter过程中如果要做一些有逻辑性的验证通常需要写一些脚本,在写脚本过程中就特别需要知道jmeter有哪些方法可供使用.

比如需求如下:有一个http的请求,每次请求参数可变,现在要求:

1.获取到请求的参数,并新增一个参数重新请求

2.获取到请求返回值并判断是否返回了code:200

下面一点点解析如何去做:

1).首先既然是http请求就添加一个HTTP 请求的 Sampler,然后将域名,端口,请求参数等填好,既然参数可变就不写死参数的值,使用参数变量实现即可(先添加一个用户自定义参数的配置元件,新增各个参数,如新增了para,那么http请求的Smapler中变量para的值直接用${para}获取到).

2)既然已经添加好了http请求的Smapler接下来分析要实现的第一个需求:获取到请求的参数,并新增一个参数重新请求.既然是要获取请求参数并且要添加参数肯定是需要在请求发出去之前添加的,所以给http Sampler添加个前置处理器,在这儿我选择了BeanShell PreProcessor,因为初次使用不知道怎么获取参数,于是只能寻求API帮助,BeanShell PreProcessor的API地址:http://jmeter.apache.org/usermanual/component_reference.html#BeanShell_PreProcessor,查看后发现提供了几个变量,其中:

sampler - (Sampler)- gives access to the current sampler
看上去是能提供当前Sampler的一些信息,继续打开Sampler发现是一个接口,看到有很多类实现了该接口,此sampler对象到底是哪个类的实例对象呢.因为目前在http Sampler里因此应该是HTTPSampler的实例对象,查看HTTPSampler有哪些方法时发现并没有提供获取参数相关的方法,再看发现有个HTTPSamplerBase基类,查看该类方法后发现有个

getPath() 
方法看上去能实现我的需求,使用sampler.getPath()后果然成功获取到了path,接下来就把要添加的参数添加后直接再sampler.setPath(path+"&sign="+sign);即可.到此需求一已经完成
3)接下去继续实现需求二:获取到请求返回值并判断是否返回了code:200,需求二是获取返回值的,因此是在请求发出后获取,因此添加个后置处理器,这儿选择的是:BeanShell PostProcessor,通过查看API发现有个

prev - (SampleResult) - gives access to the previous SampleResult (if any)
看上去是获取result的,查看SampleResult有个

getResponseDataAsString()
通过prev.getResponseDataAsString()确实获取到了返回值,因为返回值是json的,通过json解析出来code的值放到变量code里,然后增加BeanShell断言进行判断即可

至此,两个需求都已经实现! end!



---------------------
