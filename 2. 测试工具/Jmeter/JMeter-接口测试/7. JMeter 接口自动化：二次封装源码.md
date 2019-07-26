JMeter 接口自动化：二次封装源码

转自：测试窝公众号

![img](http://mmbiz.qpic.cn/mmbiz_jpg/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2swv4p25sHUrybKUaBlY0AOuzqf85WotAS2quLRHiaCeZfqpNze7vGXVA/640?wx_fmt=jpeg&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

JMeter纯Java开发，界面也是基于Swing或AWT搞出来的，所以想更深层次的去了解这款工具或对于想了解JMeter插件开发或二次开发的童鞋们来说，读读JMeter的源码估计是必不可少的，所以首先就得把源码整合起来，方便后面的Debug和二次开发，下面整理了关于JMeter源码整合到Eclipse中的一个过程，希望对大家有一定的帮助。

\1. 首先下载源文件：http://jmeter.apache.org/download_jmeter.cgi

\2. 然后解压下载的源码文件到本地路劲下面；

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2svuZZzBbeicOUtG5BiaYLgk808uZRkbvxl0WW8aclH1V9UM1s5EG7Pa8A/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

\3. 打开Eclipse，建立一个JavaProject，按照下面的截图建立JavaProject；

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2sAURLma8btWtQLVuibgXUhl56HymGiaCT1m2UDHcZCh0kumurYa6kbEFw/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2sqnlLRWoeXjRTicaGJyu8szFy763SHZSKp8EIgF5sHQialkxMVQJNIjVQ/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

\4. 选择创建的Java项目，右键选择Import，然后选择“File System”

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2s7ElFswNps8d12oPswjGYLCoSG72tgO8UCWBbpOKBqMh4s62oXTbW1g/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

\5. 定位到解压到本地路劲下的JMeter源码文件下，选择全部导入

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2siakRPguC6uvaibhpGkcOibtWwEvO1icWXsOHIkWG8HyK0OOYrX3EkcZXDw/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

\6. 导入完成后，查看项目，全部是红色的X，这个太令人苦恼了，造成出现错误的原因是没有导入对应的jar包，

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2spk38I3eAAUrEV8BPWSYgqz0EzWcKpKlNFAB8ARb1XBicrKjRX0q1Bmg/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

\7. 我们可以利用Ant下载项目所需要的jar文件，操作步骤如下：

Eclipse –> Window –> Show View –> Ant, 将ant加入Eclipse的控制面板上，

然后选择Ant面板上的添加Buildfiles;

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2s2DUgibicyH8K1UIwGchLNoDAYsicoXoTvibDH85fbNVz7UUezibiclsSaLwA/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

\8. 选择该项目下的ant的构建文件“build.xml”

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2sF3ggvT2GodibeVZ5sFLut1tg44C5gT0IRu2XTyLsdXXiaKEZ2CWDXcFg/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

\9. 在ant页面上双击“download_jars”，这个时候ant运行自动下载缺省的jar文件到项目下指定的路劲下，直到显示Build Success（这个过程中可能出现一些问题，由于网络受限，部分jar包无法下载，可能需要FQ，如果不想麻烦的，我稍后直接将所需要的jar包上传到云盘中，直接将jar包丢到lib下）

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2syVXBJ9OKlzGuibdkTlKmdHHbAWAHRoiabHw7Hae5laeQibM7KN7NAwZgw/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2syd3zaaIJozzjYlT7HVV3nkexejomajIg2G0oxaIZE4dwUH2gMrpfUA/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

\10. jar包下载完或直接将jar包已经复制到Lib文件夹下以后，这个时候右键选择项目 Build Path –> configure build path，然后选择“Add Jars”,

将lib下面所有的jar包都导入到项目中，jar包在lib下面的目录结构如下图所示，这个不能错（如果是用ant下载的，创建的目录结构就是这样的，如果自己下载jar包导入的，一定要注意lib下面的目录结构）。

jar包下载地址：https://yunpan.cn/crdkymEH8hD49（提取码：3b14）

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2s3ibN9rBk03CDTicox6PvicmJMaib6qC1hMSp00C1f3FD1BibHwErbT15eRw/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

\11. 导入成功以后再次查看项目，就可以看到万恶的红X都没有了，但是这个时候项目还不能成功运行，还需要进行下面的操作，

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2sJNJsrStQUQH4MkQ3h3lSg8HAFk3icESUriaYaibRHdeiaztOxoLelu4EEQ/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

\12. 关闭Eclipse, 进入到该Java项目的本地文件夹下，需要删除原有的“.classpath”，将“eclipse.classpath”重命名为“.classpath”，由于Windows本身的限制，所以需要进入到CMD下面进行对应的操作，如下图所示：

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2sJf8EUKxLGicdpSXBbWva6cicPSZuRQ3akn9cwcqMmtY0NItJj4HHp0oA/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2srMdiaTYPav3We4ZI8VicoXNGekCM38z8NjIROibA6rOj6ET76wtLicDVwA/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

\13. 上述操作完成后，重新打开Eclipse，找到项目的入口，

main方法，/jmeter/src/core/org/apache/jmeter/NewDriver.java

运行该项目发现还是报错，如下图所示，各种Google，各种尝试，都没有解决，其实错误的原因说的非常明确，就是bin下面缺少“jmeter.properties”,万万没想到啊，完整的项目导入为什么就没有将该配置文件导入呢？想不通，只要到源码文件中的bin目录下将该配置文件复制到项目的bin目录下，就解决了这个问题一大步了，操作如下图所示：

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2sqE2Fichr0PQWJ603UdmiahIkhUiccL8xevhX8pz7atVhKabzLlBbx4V4w/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2sFz6O5C535DSzWmaPezUsWZDtSVTJ4vsgsjlZZ4T9Q0d1AicxmiadxichQ/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2sdibjVR7IqVibKxR0Rj44nuicNNor1XrkxbpIialnaGmw8Q3oX4zRbkyTZA/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

\14. 上述操作完成以后，再次选择该项目，右键选择NewDriver这个main类， 选择 Run As –> Run Configuration, 按照下面的操作，就完美的解决了上面的报错。

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2scyUviasSeajTwOJiaC4tDoWJjfegEic7OQ6dfx8MlhcMMKTPvP4GdW4lw/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2sUB7vInQxIBEAN5LeO9J2WnKkRb0RtIJicMibt2qJxtf3nXAsVBlz0Eyg/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)

\15. 完成上面所有的操作以后，就可以尽情的各种Debug和二次开发操作了。

![img](http://mmbiz.qpic.cn/mmbiz_png/PiawwUJmG3FMVSdFVWpSIib71QwasYXc2ssEwUKduY5ibykPLvhc8LEHfoXiaiaoAbotdiaoKEAIe5rYgYovapsHU5zg/640?wx_fmt=png&mprfK=http%3A%2F%2Fmp.weixin.qq.com%2F)