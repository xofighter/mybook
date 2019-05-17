@[TOC](在Ubuntu中添加定时任务——内存释放)

> 参考文章 ：
> https://blog.csdn.net/o4dc8ojo7zl6/article/details/81199257
> https://www.cnblogs.com/michaelzhao10/p/4875719

## 问题
在Ubuntu系统中部署好测试环境后，发现内存不够用，缓冲总是被占用，无法实时释放缓存。

---
## 解决思路
通过“定时任务”功能与“内存释放”功能来定时释放内存，需要通过脚本来实现。以下是介绍定时任务命令的基本使用

### 1.定时任务
首先
**命令：** `crontab`
**命令相关参数**
|参数|含义|
|--|--|
|-e|修改用户定时任务|
|-l|列出用户的定时任务|
|-r|删除用户定时任务|
|-i|删除定时任务前通知用户|

**定时任务设置参数的基本格式 **

![img](https://ss.csdn.net/p?https://mmbiz.qpic.cn/mmbiz_png/8rlpLIcBeicHYnO9ib3ZacO2uOFic0maTxY2mn5JibTIzia4ZkZPcaGYKEyCtqKkDDPp2xq2g4QIN5Eg7Vo82VZklaw/640?wx_fmt=png)

```shell
每五分钟执行     */5 * * * *

每小时执行     	0 * * * *

每天执行         0 0 * * *

每周执行         0 0 * * 0

每月执行         0 0 1 * *

每年执行         0 0 1 1 *
```

**实例1**

![640?wx_fmt=png](https://ss.csdn.net/p?https://mmbiz.qpic.cn/mmbiz_png/8rlpLIcBeicHYnO9ib3ZacO2uOFic0maTxYpP0iahU8qiaw6JPljiapKhpnHNF1qCBqicJd1c9zmpRfia8bFqKKUXdeaLg/640?wx_fmt=png)

**实例2**

![640?wx_fmt=png](https://ss.csdn.net/p?https://mmbiz.qpic.cn/mmbiz_png/8rlpLIcBeicHYnO9ib3ZacO2uOFic0maTxYeYYCklhOAe521sdTm5yotTau7U1ia6CWqt2n94fqAjA3zZNTV2vBByQ/640?wx_fmt=png)

### 2.内存释放

**命令：**

用于linux系统 （RedHat CentOS CGSL ubuntu）

![png](https://ss.csdn.net/p?https://mmbiz.qpic.cn/mmbiz_png/8rlpLIcBeicHYnO9ib3ZacO2uOFic0maTxYwV3JCx6u3X44qQIgOOOCE3Z94rABWsiciaXKopCP4Mr8T55ZnUALEjcA/640?wx_fmt=png)

- sync 做同步，将所有未写的系统缓冲区写到磁盘中，以确保文件系统的完整性，否则在释放缓存的过程中，可能会丢失未保存的文件
- 1 释放页缓存
- 2 释放dentries和inodes
- 3 释放所有缓存

---
## 实际操作
了解完命令，就开始具体操作吧~

### 1.内存释放脚本

1. 初级版本

   root用户下编写脚本

![640?wx_fmt=png](https://ss.csdn.net/p?https://mmbiz.qpic.cn/mmbiz_png/8rlpLIcBeicHYnO9ib3ZacO2uOFic0maTxYSk5Z9QxqfcJVibBnaYEex0AKwWicSDQKrpmwbSwoGbvgsQeEicAc6c4SA/640?wx_fmt=png)

2. 高级版本

在根目录创建`releasememory.sh`，然后把下面的代码贴进去，保存。
该脚本作用是:查询`free`内存比例，少于20%就清空缓存。

**脚本如下**

```shell
#!/bin/bash
mem_total=`free -m | awk 'NR==2' | awk '{print $2}'`
mem_free=`free -m | awk 'NR==2' | awk '{print $4}'`
mem_used=`free -m | grep Mem | awk '{print  $3}'`

echo "===========================" >> /var/log/mem.log
date >> /var/log/mem.log
echo "Memory usage | [Total：${mem_total}MB][Free：${mem_free}MB][Used：${mem_used}MB]" >> /var/log/mem.log

if (($mem_used != 0)); then
mem_per=0`echo "scale=2;$mem_free/$mem_total" | bc`
DATA="$(date -d "today" +"%Y-%m-%d-%H-%M") free percent is : $mem_per"
echo $DATA >>/var/log/mem_detect.log
mem_warn=0.20
mem_now=`expr $mem_per \> $mem_warn`
if (($mem_now == 0)); then
sync
echo 3 > /proc/sys/vm/drop_caches
echo "OK" >> /var/log/mem.log
fi
fi
```
**给脚本执行权限**

`# chmod +x releasememory.sh`


### 2.开启crontab日志

1. root用户下执行crontab -e命令

![png](https://ss.csdn.net/p?https://mmbiz.qpic.cn/mmbiz_png/8rlpLIcBeicHYnO9ib3ZacO2uOFic0maTxYxXmO7jh9KNjicYw3yjFkCk9PicTw046tl1Ev0850WP0ibga7hUhqHiaPhg/640?wx_fmt=png)

**需要注意的是：anon的命令，ctrl+o是保存，ctrl+x是退出**

2. 查看设置情况

   ```shell
   [root@ems001 home]# crontab -l
   
   */1 * * * * /home/clean_mem.sh
   
   [root@ems001 home]#
   ```

**需要注意的是我们通过root用户执行的crontab命令，则该定时任务的规则仅适用于root用户，所以脚本的属组为root用户**

crontab默认情况下是开启不执行的，所以一开始写完后，不能执行，想查看日志，却找不到。所以先开启定时任务的日志来查看

　　修改rsyslog服务，将 /etc/rsyslog.d/50-default.conf  文件中的 #cron.* 前的 # 删掉；用以下命令重启rsyslog服务：

```
service rsyslog restart；
```

　　然后再重启crontab服务：

```
service cron restart；
```

　　网上很多人说开启，停止或者重启contab服务用命令 service crond restart,执行这个命令会报出 unrecognised service ,这里吧 crond 改成 cron 就可以了。

　　之后，在这个位置 /var/log/cron.log 就可以查看定时任务的文件日志文件了；

　　下面这个命令还可以查看crontab的状态

```
service cron status；
```

　　查看运行时的日志文件，如果在日志文件中执行一条语句后出现：

　　No MTA installed, discarding output
　　则crontab执行脚本时是不会直接错误的信息输出，而是会以邮件的形式发送到邮箱里，需要邮件服务器了，如果没有安装邮件服务器，它就会报这个错。

　　可以在每条定时脚本后面加入：

```
>/dev/null 2>&1
```

　　即将所有信息输入到linux系统中的空设备/dev/null中；即可解决No MTA installed, discarding output的问题。

 　　在最终能用之前我还运行过以下命令，是给root赋权限。但是在我最终能运行时，我不知道这条命令有没有起作用。不过还是先写上。运行后需要重启crontab服务

```
crontab -u root /var/spool/cron/crontabs/root
```

　　之前不能运行的时候，看到很多人说不能运行是环境变量的问题，需要修改。不过我这次并没有遇到。不过先记下来。以后可能会用到。

### 3.查看定时任务的执行情况

定时任务的执行日志记录在/var/log/cron中

[root@ems001 home]# cd /var/log/

[root@ems001 log]# tail -f cron

Jul 24 10:37:01 ems001 CROND[19842]: (root) CMD (/home/clean_mem.sh)

Jul 24 10:38:02 ems001 CROND[19850]: (root) CMD (/home/clean_mem.sh)

Jul 24 10:39:01 ems001 CROND[19854]: (root) CMD (/home/clean_mem.sh)

Jul 24 10:40:01 ems001 CROND[19862]: (root) CMD (/home/clean_mem.sh)

查看内存释放情况

[root@ems001 home]# free

```
    total       used       free     shared    buffers     cached
```

Mem:      24554900    5807180   18747720      16232       1276    1827572

-/+ buffers/cache:    3978332   20576568

Swap:     18874364          0   18874364

[root@ems001 home]# free

```
    total       used       free     shared    buffers     cached
```

Mem:      24554900    4980332   19574568      16232       1300     999016

-/+ buffers/cache:    3980016   20574884

Swap:     18874364          0   18874364

两次对比可以看出释放了1G的内存

这样一个自动清理内存的设置就完成了

### 4.删除定时任务

------

![640?wx_fmt=png](https://ss.csdn.net/p?https://mmbiz.qpic.cn/mmbiz_png/8rlpLIcBeicHYnO9ib3ZacO2uOFic0maTxYDbpWKjuYRiaWMl1VHpAj4mYRqPltHKgU20PL9bzmuzgxtb6cFyx8XIg/640?wx_fmt=png)



　　















