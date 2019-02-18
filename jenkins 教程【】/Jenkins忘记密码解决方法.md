1、没有修改过密码的情况下找回初始密码（或者第一次部署的时候）

在ubuntu下使用root账户命令进入 /root/.jenkins/secrets目录下面，找到initialAdminPassword文件

使用命令查看该文件密码 cat initialAdminPassword



![img](https:////upload-images.jianshu.io/upload_images/6323765-22e383409b9a7e80.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/722/format/webp)



在登录界面，输入登录名admin，密码就是上面查到的一串字符，进行登录





![img](https:////upload-images.jianshu.io/upload_images/6323765-fc1a35ccbaa78cd3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/551/format/webp)



2、admin密码修改过后忘记的情况

（1）ubuntu下使用命令进入/root/.jenkins目录下面找到config.xml文件

使用命令 sudo vi /root/.jenkins/config.xml进行编辑，删除去掉下面整段的代码片段



![img](https:////upload-images.jianshu.io/upload_images/6323765-20dc462d7fb89796.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



（2）删除后进行保存后，重新启动linux系统，会进入到下面的界面，如图所示：





![img](https:////upload-images.jianshu.io/upload_images/6323765-a8afe673496b4d59.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



（3）接着进入“系统管理”-“Configure Global Security”进行设置，如图所示，设置完成后进行保存



![img](https:////upload-images.jianshu.io/upload_images/6323765-7c757e2379fd8dae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



（4）接着再次进入“系统管理”-“管理用户”，点击右侧的设置进行修改密码进行保存，如果所示：





![img](https:////upload-images.jianshu.io/upload_images/6323765-1b2983ab6656a395.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



（5）在设置界面最下方进行修改密码后保存就完成了设置，如图所示





![img](https:////upload-images.jianshu.io/upload_images/6323765-24cdd1595c3de420.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/797/format/webp)



（6）最后就可以用刚设置的密码进行登录了,为了安全最后进行两项设置1、是去掉运行新用户注册 2、选择登录用户可以做任何事情





![img](https:////upload-images.jianshu.io/upload_images/6323765-421b21bea67bf1bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)





作者：测试_机器猫

链接：https://www.jianshu.com/p/670a7a52af14

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。