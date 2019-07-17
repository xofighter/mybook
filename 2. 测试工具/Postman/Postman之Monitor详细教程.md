版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/testdeveloper/article/details/80679988

- Monitor简介

1、是基于Postman集合API的灵活监控



2、监控API的正常运行时间、响应能力和正确性



3、提供监测结果的详细报告



4、对所有Postman用户每月提供1000个免费的监控请求

- 详细使用教程

1、点击需要监控集合右侧箭头，选择Monitors，点击“Add a monitor”

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/de8001193a9e47a2b87924feb48bcf87/36aea46c6ef042409c3d5c8be18fa2ea.png)



2、输入监控名称“机票 Monitor”，选择运行环境“online”，定时类别我选择了分钟级的“Minute Timer”，每5分钟运行一次

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/bafbe5d2b09c499195a3f483b498c925/8c4458f2bae043ee892e0a9313aec313.png)



3、添加成功后，点击去集合的Monitors下查找你的监控，点击后跳转到web页面查看监控报告

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/176b6e1e292f4bc9ab22e1618909db7a/0d67514cf5ce460e91261fdadf7d249f.png)



4、监控报告提供了两种视图模式，Monitor Summary 和 Request Split，切换视图模式变化的是响应时间的统计方式；



View 可以查看单个接口的响应时间；底部的TestResults和Console log可以查看具体的接口测试结果

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/aff9d183e1a44ec5b757630384552a4b/7a085df8baca42f2ac1053dce1a40e17.png)