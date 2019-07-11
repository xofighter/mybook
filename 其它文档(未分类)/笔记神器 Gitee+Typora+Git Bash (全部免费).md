Gitee(码云)相比于github的优势:
免费用户也可以创建私有目录

5G存储空间(GitHub貌似推荐存储空间在1G以内)

同步速度非常快

这种方式令人惊喜的地方:
github目录可以直接转移到码云.

本地目录可以同时添加GitHub和Gitee远程仓库.

Gitee支持给文档打标签

typora对markdown和latex的支持很完美

这一套都是免费的

　　

具体步骤:
添加git生成的ssh key到码云:
​	由于之前已经配置过git, 在windows下, 我的ssk key在C:\Users\sky_73\ .ssh\id_rsa.pub


从GitHub下载到本地的目录,再同步到码云:
先给目录添加码云的远程代码库, 参考step1 git remote add gitee git@gitee.com:hongbozhang/reponame.git 推送到码云, 参考step2

也可以在Gitee个人主页使用"从Github导入项目"功能, 前提是Gitee和Github账号已经绑定:

 

本地目录同步到码云:
在码云上新建和本地目录local_directory同名的目录

cd local_directory

git init

git add -A

git commit -m "your comment"

git push gitee master


参考步骤:
step1.给目录添加远程代码仓库, 关联码云:
 cd 到git目录

 git init

 git remote add gitee git@gitee.com:hongbozhang/目录名.git

 git remote -v //查看远程库信息:


或者新建一个git目录:
 mkdir folder_name

 cd folder_name

 git init

 git add file.xxx (或者用git add -A添加所有(有变化)文件)

 git commit -m "your comment"


step2. 将新目录或改动同步到码云:
​	在码云上建立相同目录名的目录

	同步:
	
	git push gitee master
---------------------
参考:

[廖雪峰使用码云](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00150154460073692d151e784de4d718c67ce836f72c7c4000)

[廖雪峰git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

作者：qq1491599481 
来源：CSDN 
原文：https://blog.csdn.net/qq1491599481/article/details/80999561 
版权声明：本文为博主原创文章，转载请附上博文链接！