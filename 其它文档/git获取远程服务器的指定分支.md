# [git获取远程服务器的指定分支](https://www.cnblogs.com/phpper/p/7136048.html)



昨天糗大了...进入新公司，公司服务器上有Online为线上版本，开发版本默认的为Master，本地clone的开发版为master,公司用的git 自动部署(puh后服务器自动更新了代码...这个有很多配置教程，这里就不赘述啦),
本地开发分支master,一般流程是 合并到online分支后，把online push到远程分支....这个是流程OK不说了
场景是这样的：
本地master
执行 checkout -b online后
git push origin online  发现权限不够....囧事发生了，我以为是账号权限受控....特意去问cto......
好吧，我这里操作是不对滴(按照以往的部署方式不能习惯性的用在新公司..警惕吧)
git pull 操作就可以从远程库中获取某个分支的更新，再与本地指定的分支进行自动merge(即使本地不存在这个分支)

完整格式是：

```
$ git pull <远程库名> <远程分支名>:<本地分支名>
```

比如，取回远程库中的online分支，与本地的online分支进行merge，要写成：

```
git pull origin online:online
```

如果是要与本地当前分支merge，则冒号后面的<本地分支名>可以不写

```
git pull origin online
```

通常，git会将本地库分支与远程分支之间建立一种追踪关系。比如，在git clone的时候，所有本地分支默认与远程库的同名分支建立追踪关系。也就是说，本地的master分支自动追踪origin/master分支。因此，如果当前处于本地online分支上，并且本地online分支与远程的online分支有追踪关系，那么远程的分支名可以省略：

比如，取回远程库中的online分支，与本地的online分支进行merge，要写成：

```
git pull origin 
```

其实，git pull 命令等同于先做了git fetch ，再做了git merge。即：

```
git fetch origin online
git checkout online
git merge origin/online
```

造成我的错误在于我本地使用checkout -b online 是创建的新onlne分支，而与服务器online根本不算是同一个分支，所以要从服务端拉取，切记吧

再次完整总结这次教训：

### git 拉取远程指定分支 pull本地不存在的分支

默认，如果git项目只有一个分支，就是master,我们当然可以在本地创建多个分支，并推送到远程git管理平台上，或者将远程git管理平台上的其他分支拉取到自己电脑上。

##### 一、查看本地已有的分支

进入到项目根目录，打开命令行/终端，输入指令，将会显示该项目的本地的全部分支，其中、当前分支的前面有*号。

```
git branch
```

![img](https://images2015.cnblogs.com/blog/1102222/201707/1102222-20170708104010034-1822343874.png)

##### 二、本地检出一个新的分支并推送到远程仓库

###### （一）.创建本地分支

```
git checkout -b 新分支名
```

执行该指令后，会在本地创建一个新分支，该分支是从当前分支上检出的，所以所有文件内容都和当前分支一模一样，这是正常的。创建成功后，将自动切换至新分支上。

比如我要创建一个名为dev1的新分支：

```
git checkout -b dev1
```

![img](https://images2015.cnblogs.com/blog/1102222/201707/1102222-20170708104227987-394785598.png)

此时，再执行git branch查看当前本地所有分支，就会看到多了个分支：dev1.

![img](https://images2015.cnblogs.com/blog/1102222/201707/1102222-20170708104323644-2029973583.png)

##### （二）.推送本地分支到远程仓库

```
git push --set-upstream origin 分支名
```

例如，我要把上一步创建的本地dev1推送到远程仓库：

![img](https://images2015.cnblogs.com/blog/1102222/201707/1102222-20170708104607409-2041850776.png)

##### 三、将远程git仓库里的指定分支拉取到本地（本地不存在的分支,今天的囧事就发生在这个环节）

当我想从远程仓库里拉取一条本地不存在的分支时：

```
git checkout -b 本地分支名 origin/远程分支名
```

这个将会自动创建一个新的本地分支，并与指定的远程分支关联起来。

例如远程仓库里有个分支dev2,我本地没有该分支，我要把dev2拉到我本地：

![img](https://images2015.cnblogs.com/blog/1102222/201707/1102222-20170708105204269-529573918.png)

若成功，将会在本地创建新分支dev2,并自动切到dev2上。

如果出现提示：

```
fatal: Cannot update paths and switch to branch 'dev2' at the same time.
Did you intend to checkout 'origin/dev2' which can not be resolved as commit?
```

表示拉取不成功。上面的我需要先执行

```
git fetch
```

然后再执行

```
git checkout -b 本地分支名 origin/远程分支名
```

即可。
![img](https://images2015.cnblogs.com/blog/1102222/201707/1102222-20170708105409159-596290568.png)

其实这里就是我上面描述的，不用fetch,直接 git pull origin dev2:dev2 一个命令可以达到同等效果.
![img](https://images2015.cnblogs.com/blog/1102222/201707/1102222-20170708105625644-336027771.png)

提示已经是最新的了...

无论从事什么行业，只要做好两件事就够了，一个是你的专业、一个是你的人品，专业决定了你的存在，人品决定了你的人脉，剩下的就是坚持，用善良專業和真诚赢取更多的信任。