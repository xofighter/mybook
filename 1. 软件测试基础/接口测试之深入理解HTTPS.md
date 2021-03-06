[前言]()

随着网络安全问题越来越被重视，HTTPS协议的使用已经逐渐主流化。目前的主流站点均已使用了HTTPS协议；比如：百度、淘宝、京东等一二线主站都已经迁移到HTTPS服务之上。而作为测试人员来讲，也要需时俱进对HTTPS协议要有一定的了解，这样就可以更好的帮助我们在工作完成任务和排查问题。

[HTTP与TCP]()

首先，在理解HTTPS之前需要了解HTTP。HTTP（Hyper Text Transfer Protocol）是超文本传输协议，是在互联网上广泛使用的一种信息传输方式，通常浏览器与服务器进行通信使用的都是HTTP协议。该协议因为其使用简单、方便，随着互联网的发展也同时在壮大。而HTTP协议本身只是定义了一份规范，具体的传输能力则是由TCP协议来完成。所以HTTP是基于TCP之上，广泛应用与浏览器中的一种传输协议。

[HTTP与HTTPS]()

因为有了HTTP协议，所以互联网的发展如虎添翼，在短短几十年时间内，改变了人类很多的生活方式甚至生存方式。至今HTTP本身仍然是可以支持互联网的通信，只是其在安全上不能提供很好的保障。尤其是互联网与人们的生活越来越密切的时候，互联网的安全问题也越来越重要。而HTTPS则是解决这个问题的方法。本质上HTTPS=HTTP+SSL/TLS。其中SSL和TLS是一种加密协议，即HTTPS是一种带加密功能的HTTP协议。

[HTTPS、HTTP、TCP之间的关系]()



![img](https:////upload-images.jianshu.io/upload_images/11349666-efcc345ce72ba9f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



在这里向大家推荐一个学习资料分享群：747981058

即HTTPS并不是一个新的协议，而是多个协议组装后的产物。而其安全性则主要由SSL/TLS层来保障。（当然SSL/TLS也可以可以保障其它的应用层协议，比如：SSL+FTP=SFTP）

[SSL/TLS的安全机制原理]()

既然SSL可以保障安全，那么它到底是如何保障的呢？其实SSL安全机制是通过对HTTP协议内容进行加密来实现的。而加密方式一般有2种类型：对称加密和非对称加密。

对称加密：加密和解密时使用的密匙(key)是一样的。密匙安全性不高，加密速度快

非对称加密：加密时使用公匙加密，解密时使用私匙解密。密匙安全性高，加密速度相对慢

而SSL中两者都有使用，且分别结合了它们的优缺点。在每次通信前先使用非对称加密来确定本次通信后续使用的对称加密的密匙，之后所有的通信都是使用临时生成的密匙来进行对称加密后传输。

但是从安全角度考虑这还不够，因为密匙还是有可能被人窃取了，然后篡改内容。所以就引入了证书机制，相当于给加密内容在加一个盖章。接着问题就变成了有人伪造证书怎么办？再然后就有了第三方认证机构，专门来发证书的，只有这些机构发的证书才可以信任。

于是现在访问HTTPS网站的时候就可能有两种情况，一种是使用认证的证书、一种是使用未认证的证书。当我们访问的网站使用未认证的证书时，浏览器往往就会有提醒--该网站不可信任。如果要一意孤行，就需要自己手动点继续。（当然你还可以给浏览器设置忽略安全认证）



![img](https:////upload-images.jianshu.io/upload_images/11349666-cfb7ff469f36a2d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/950/format/webp)



[证书发放与安装]()

全世界范围内可以发放安全证书的第三方机构就2-3个，而全世界有那么多的公司或网站需要申请证书。如何知道某个证书是否可信任呢？

其实安全证书有分根证书、子证书、子子证书，不同等级的信任范围。通常根证书是由最上面的第三方机构颁发给自己的，根证书下面的一级子证书通常是颁发给其下的代理公司的，而如果你的网站是从代理公司申请的，那么你证书将会是一个二级子证书。

而在认证证书的时候， 操作系统或者程序会去检查该证书此前是否已经被信任过，或者该证书的上级证书（父级、父父级等等）是否被信任过。只要有一个等级的证书被信任过，则认为该证书是可信任的。

具体证书是否可信任是根据系统或者程序是否已安装并信任了该证书。通常操作系统都会预装顶级证书机构的根证书，所以只要你访问的网站证书是从这几个顶级第三方机构或其代理申请的，那么就会被直接信任无需你去手动下载和安装。

[HTTPS代理]()

了解了HTTPS和证书的基本知识之后，再来看看HTTPS的代理是如何实现的。与HTTP代理相比，HTTPS代理会多一个证书管理的功能，因为它要给浏览器端下发证书，才能模拟正常的HTTPS连接。所以HTTPS代理在进行内容转发之前，还需要对内容进行解码和加密的转换。具体图解如下：





![img](https:////upload-images.jianshu.io/upload_images/11349666-46bd6c3993f7db37.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/953/format/webp)



这就是为什么使用Charles、Fiddler之类的软件代理HTTPS的时候，需要先安装一个它们的证书（尤其是移动端）。因为它们的证书是没有认证过的，需要手动安装并信任，之后才可以正常的代理。

[结语：]()

跟大家推荐一个学习资料分享群：**747981058**，里面大牛已经为我们整理好了许多的学习资料，有自动化，接口，性能等等的学习资料！人生是一个逆水行舟的过程，不进则退，咱们一起加油吧！

作者：软件测试学习提升

链接：https://www.jianshu.com/p/01288d691dc6

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。