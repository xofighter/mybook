

一、fiddler的下载和安装

二、fiddler的配置设置

三、APP抓包时的手机代理设置

一、fiddler的下载和安装
参照我的另一博文 ：【fiddler】fiddler的下载和安装

博文地址：https://blog.csdn.net/qq_39720249/article/details/80724652       点击前往博文

二、fiddler的配置设置
（一）打开fiddler的配置菜单Options：

1.点击fiddler的安装目录中的fiddler.exe文件，打开fiddler软件。
2.找到tools菜单，选中Options...，单击打开Options设置页面。

![img](https://img-blog.csdn.net/20180716191336765?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


（二）HTTPS配置设置：

1.点击打开：Options → HTTPS，默认已勾选Capture HTTPS CONNECTs 。

2.勾选：Decrypt HTTPS traffic；
  下拉列表选择：...from all processes；
  勾选：Ignore server certificate errors（unsafe）。

3.点击OK保存设置。
注意：此部分操作，有出现问题的，参考我的另一博文：

         【fiddler】fiddler设置：设置HTTPS勾选了Decrypt HTTPS traffic时出错情况和解决方法

博文地址：https://blog.csdn.net/qq_39720249/article/details/81071307    点击前往博文

![img](https://img-blog.csdn.net/20180716194515150?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/20180716222339417?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdnimg.cn/20181222161453713.png)

![img](https://img-blog.csdnimg.cn/20181222161502656.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/20181222161510542.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/20181222161518330.png)

![img](https://img-blog.csdn.net/20180716222339417?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

HTTPS下拉列表的一些选项说明：

1.from all processes : 抓取所有的 https 程序, 包括 PC本机 和 手机。

2.from browsers only : 只抓取浏览器中的 https 请求。

3.from non-browsers only : 只抓取除了浏览器之外的所有 https 请求。

4.from remote clients only ： 抓取远程的客户端的 https ,可以代表手机，开启手机 Https 抓包，
                              如果只想抓取手机上的所有 https 请求，就应该选中此选项。
（三）Connections配置设置：

1.点击打开：Options → Connections。

2.勾选：Allow remote computers to connect，弹窗点击“确定”后，点击“OK”保存设置。

![img](https://img-blog.csdn.net/20180716223803254?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/201807162238190?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


（四）fiddler证书需安装至根证书：

没有安装fidder根证书会出现的情况：

1.设置好fidder的options之后，会发现fidder安全证书安装在“个人”证书下，未验证安全性，证书提示需要安装在根证书下。

2.打开fidder，浏览器访问https://www.baidu.com网址时，提示“你的连接不是私密连接”，结果无法访问。

3.可以成功访问百度网址，但是浏览器下载软件发现迟迟连接不上，无法下载。

![img](https://img-blog.csdn.net/20180717104608612?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/20180717104628969?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


安装fiddler根证书步骤：

1.打开fiddler-->tool-->Options-->HTTPS

2.勾选：Check for certificate revocation

3.点击右边的按钮：Actions

4.信任根证书，单击选择：Trust Root Certificate

5.出现警告弹窗，均选择yes

6.回到Https的页面，最后再OK，重启一下fiddler就可以了。

7.如果重启fiddler后，打开浏览器网址依旧连接失败:
  那就重置证书，再次信任根证书，保存后，关闭fiddler和浏览器，关闭计算机,重启计算机后再操作。

8.PC端浏览器访问代理服务器成功，可以抓取浏览器的http和https的请求了。

![img](https://img-blog.csdn.net/20180717110211500?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdnimg.cn/20181222170731114.png)![img](https://img-blog.csdnimg.cn/20181222170740328.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/2018122217074822.png)![img](https://img-blog.csdnimg.cn/20181222170834384.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)![img](https://img-blog.csdn.net/20180717111532759?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)








三、APP抓包时的手机代理设置
（一）环境准备：

让手机和PC在同一个局域网下面：
1.如果PC是笔记本，让iOS或Android手机、iPhone或笔记本它们连接同一个wifi网络即可。

2.如果PC是网线连接的台式电脑，则在这个局域网下找到一个WiFi：
让iOS或Android手机、iPhone，连接此WiFi，开启代理设置，而IP则设置本机IP地址。
PC电脑本机IP的确定方法：
1.打开cmd窗口，输入命令:ipconfig ，IPv4地址就是本机IP地址。

2.点击fidder.exe文件打开fidder，根据fidder的Online菜单，可以找到本机IP地址。

![img](https://img-blog.csdn.net/20180716231818319?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/20180716231833269?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


（二）手机代理设置：

1.先点击fiddler.exe文件打开fiddler，目前已设置好一打开fiddler，就表示已经成功打开PC电脑的代理服务器。

2.手机：设置 → WLAN → 找到同一局域网的WiFi  → 修改网络 

3.WiFi代理设置：输入正确的WiFi密码 → 勾选“显示高级选项” → 代理选择“手动” 

→ 服务器主机名设置为PC本机IP

→ 服务器端口8888（这个端口设置在：fidder → Options → Connections →  Fiddler listens on port）

→ 点击“连接”保存此代理服务器连接设置。

4.已设置手机代理服务器成功。

![img](https://img-blog.csdn.net/20180716234009723?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/20180716233825525?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


（三）手机fiddler证书安装：

手机安装fiddler证书步骤：

1、手机设置代理，同时设置手机锁屏密码。

2、手机浏览器，地址栏输入fiddler右上方的IP地址和端口： 
http：//IP:8888 

3、点击页面里面的：FiddlerRoot certificate 

4.下载fidder证书，记住证书的下载路径

5.手机:设置 → 高级设置 → 安全 → 从SD卡安装 → 内部存储空间 → 下载路径 → 点击下载的证书

6.点击证书安装 → 自定义证书名字（例如：fidder_该证书的IP：端口） → 点击“确定”，安装证书。

7.已设置成功，可以开始抓取手机http和https请求了。

![img](https://img-blog.csdn.net/20180717115007294?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/20180717115314247?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/2018073018340555?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



---------------------
