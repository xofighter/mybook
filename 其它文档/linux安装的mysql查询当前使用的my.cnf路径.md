my.cnf是MySQL启动时加载的配置文件，一般会放在mysql的安装目录中，用户也可以放在其他目录加载。安装mysql后，系统中会有多个my.cnf文件，有些是用于测试的。使用locate my.cnf命令可以列出所有的my.cnf文件

```shell
# 命令
locate my.cnf

# 输出

/usr/local/Cellar/mysql/5.6.24/my.cnf



/usr/local/Cellar/mysql/5.6.24/mysql-test/include/default_my.cnf



/usr/local/Cellar/mysql/5.6.24/mysql-test/suite/federated/my.cnf



/usr/local/Cellar/mysql/5.6.24/mysql-test/suite/ndb/my.cnf



/usr/local/Cellar/mysql/5.6.24/mysql-test/suite/ndb_big/my.cnf



/usr/local/Cellar/mysql/5.6.24/mysql-test/suite/ndb_binlog/my.cnf



/usr/local/Cellar/mysql/5.6.24/mysql-test/suite/ndb_rpl/my.cnf



/usr/local/Cellar/mysql/5.6.24/mysql-test/suite/ndb_team/my.cnf



/usr/local/Cellar/mysql/5.6.24/mysql-test/suite/rpl/extension/bhs/my.cnf



/usr/local/Cellar/mysql/5.6.24/mysql-test/suite/rpl/my.cnf



/usr/local/Cellar/mysql/5.6.24/mysql-test/suite/rpl_ndb/my.cnf
```

当我们需要修改配置文件时，需要找到mysql启动时是加载了哪个my.cnf文件。 
 

### 1.查看是否使用了指定目录的my.cnf

启动mysql后，我们查看mysql的进程，看看是否有设置使用指定目录的my.cnf文件，如果有则表示mysql启动时是加载了这个配置文件。

```shell
# 命令
ps aux|grep mysql|grep 'my.cnf'

# 输出
fdipzone         25174   0.0  0.0  3087244    600   ??  S     4:12下午   0:01.14 /usr/local/Cellar/mysql/5.6.24/bin/mysqld --defaults-file=/usr/local/Cellar/mysql/5.6.24/my.cnf --basedir=/usr/local/Cellar/mysql/5.6.24 --datadir=/usr/local/var/mysql --plugin-dir=/usr/local/Cellar/mysql/5.6.24/lib/plugin --bind-address=127.0.0.1 --log-error=/usr/local/var/mysql/TerrydeMacBook-Air.local.err --pid-file=/usr/local/var/mysql/TerrydeMacBook-Air.local.pid

fdipzone         25064   0.0  0.0  2452824      4   ??  S     4:12下午   0:00.03 /bin/sh /usr/local/opt/mysql/bin/mysqld_safe --defaults-file=/usr/local/Cellar/mysql/5.6.24/my.cnf --bind-address=127.0.0.1 --datadir=/usr/local/var/mysql
```

可以看到/usr/local/Cellar/mysql/5.6.24/my.cnf就是mysql启动加载的配置文件。

如果上面的命令没有输出，表示没有设置使用指定目录的my.cnf。

### 2.查看mysql默认读取my.cnf的目录

如果没有设置使用指定目录的my.cnf，mysql启动时会读取安装目录根目录及默认目录下的my.cnf文件。

查看mysql启动时读取配置文件的默认目录

```shell
# 命令
mysql --help|grep 'my.cnf'
# 输出
                      order of preference, my.cnf, $MYSQL_TCP_PORT,

/etc/my.cnf /etc/mysql/my.cnf /usr/local/etc/my.cnf ~/.my.cnf
```

/etc/my.cnf, /etc/mysql/my.cnf, /usr/local/etc/my.cnf, ~/.my.cnf 这些就是mysql默认会搜寻my.cnf的目录，顺序排前的优先。 
 

### 3.启动时没有使用配置文件

如果没有设置使用指定目录my.cnf文件及默认读取目录没有my.cnf文件，表示mysql启动时并没有加载配置文件，而是使用默认配置。

需要修改配置，可以在mysql默认读取的目录中，创建一个my.cnf文件(例如:/etc/my.cnf)，把需要修改的配置内容写入，重启mysql后即可生效