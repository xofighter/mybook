MariaDB/MySQL的默认设置性能非常差，仅仅起一个功能测试的作用，不能用在生产环境中，因此要对一些参数进行调整优化。当然，对配置文件各参数的调整需要根据实际环境，不同时期不同数量级的数据进行性能优化。

![MariaDB/MySQL配置文件my.cnf解读](https://www.helloweba.net/attachments/contimg/2018/mysql-mariadb.jpg)

MySQL/MariaDB 配置文件位于 /etc/my.cnf。 每次更改此文件后你需要重启 MySQL 服务，以使更改生效。本文的参数配置基于MariaDB 10.2，硬件内存4G。文中一些选项值的设置只是推荐值，不要盲目的接受这些建议。每个 MySQL 设置都是不同的，在进行任何更改之前需要慎重考虑。

# 基本结构

```
[client]
port		= 3306 #客户端默认连接端口
socket		= /tmp/mysql.sock  #用于本地连接的socket套接字

[mysqld]  # 服务端基本配置
port		= 3306 # mysql监听端口
socket		= /tmp/mysql.sock #为MySQL客户端程序和服务器之间的本地通讯指定一个套接字文件
user    = mariadb # mysql启动用户
basedir = /usr/local/mariadb # 安装目录
datadir = /data/mysql # 数据库数据文件存放目录
log_error = /data/mysql/mariadb.err #记录错误日志文件
pid-file = /data/mysql/mariadb.pid  #pid所在的目录
skip-external-locking  #不使用系统锁定，要使用myisamchk,必须关闭服务器
...
```

my.cnf配置文件以方括号如`[client]`区分模块作用域，其中`[client]`表示客户端配置，`[mysqld]`是服务端配置。以上几个选项是最基础的，每个选项有简单说明，其中有关路径的配置是基于本站文章：[CentOS7下源码编译安装MariaDB 10.2](https://www.helloweba.net/server/514.html)和[Linux下使用二进制格式安装MariaDB](https://www.helloweba.net/server/515.html)的安装路径来配置的，所以在配置前可以先按照两文先安装好MairaDB。

#### 选项配置及说明

`key_buffer_size = 32M `这个参数用来缓存MyISAM存储引擎的索引参数。MySQL5.5默认为InnoDB存储引擎，所以这个参数可以设置小点，64MB即可。

`max_allowed_packet = 1M` 允许最大接收数据包的大小，防止服务器发送过大的数据包。可以设置为16MB或者更大，但设置的太大也可能有危险。

`table_open_cache = 128` #MySQL每打开一个表，都会读入一些数据到table_open_cache缓存中，当MySQL在这个缓存中找不到相应信息时，才会去磁盘上读取。默认值64，假定系统有200个并发连接，则需将此参数设置为200*N(N为每个连接所需的文件描述符数目)；当把table_open_cache设置为很大时，如果系统处理不了那么多文件描述符，那么就会出现客户端失效，连接不上。

`sort_buffer_size = 768K` 在表进行order by和group by排序操作时，由于排序的字段没有索引，会出现Using filesort，为了提高性能，可用此参数增加每个线程分配的缓冲区大小。默认为256KB，这个参数不要设置过大，一般在128～256KB即可。另外，一般出现Using filesort的时候，要通过增加索引来解决。

`net_buffer_length = 8K` 包消息缓冲区初始化为net_buffer_length字节，但需要时可以增长到max_allowed_packet字节。

`read_buffer_size = 768K` 该参数用于表的顺序扫描，表示每个线程分配的缓冲区大小。比如在进行全表扫描时，MySQL会按照数据的存储顺序依次读取数据块，每次读取的数据块首先会暂存在read_buffer_size中，当buffer空间被写满或者全部数据读取结束后，再将buffer中的数据返回给上层调用者，以提高效率。默认为128K，这个参数不要设置过大，一般在128～256之间。

`read_rnd_buffer_size = 512K` 该参数用于表的随机读取，表示每个线程分配的缓冲区大小。比如，按照一个非索引字段做order by排序操作时，就会利用这个缓冲区来暂存读取的数据。默认为256KB，这个参数不要设置过大，一般在128～512KB。

`myisam_sort_buffer_size = 8M` 当对MyISAM表执行repair table或创建索引时，用以缓存排序索引；设置太小时可能会遇到” myisam_sort_buffer_size is too small”

`thread_cache_size = 16` 线程池，线程缓存。用来缓存空闲的线程，以至于不被销毁，如果线程缓存在的空闲线程，需要重新建立新连接，则会优先调用线程池中的缓存，很快就能响应连接请求。每建立一个连接，都需要一个线程与之匹配。

`query_cache_size = 16M` 缓存select语句和结果集大小的参数。查询缓存会存储一个select查询的文本与被传送到客户端的相应结果。如果之后接收到一个相同的查询，服务器会从查询缓存中检索结果，而不是再次分析和执行这个同样的查询。如果你的环境中写操作很少，读操作频繁，那么打开query_cache_type=1，会对性能有明显提升。如果写操作频繁，则应该关闭它（query_cache_type=0）。

`tmp_table_size = 32M` 临时HEAP数据表的最大长度(默认设置是32M); 超过这个长度的临时数据表将被转换为MyISAM数据表并存入一个临时文件。

`explicit_defaults_for_timestamp = true` 是否显示默认时间戳

\#skip-networking

`max_connections = 500` 该参数用来设置最大连接数，告诉你当前你的服务器允许多少并发连接。默认为100，一般设置为512-1000即可。请记住，太多的连接会导致内存的使用量过高并且会锁住你的 MySQL 服务器。一般小网站需要 100-200 的连接数，而较大可能需要 500-800 甚至更多。这里的值很大程度上取决于你 MySQL/MariaDB 的使用情况。

`max_connect_errors = 100` 如果有时网络抽风，或者应用配置错误，或者其他原因导致客户端短时间内不断的尝试连接，客户端可能会被列入黑名单，然后将无法连接，直到再次刷新主机缓存。这个选项默认值太小了，可以考虑设的足够大（如果你的服务器配置够强大的话）。

`open_files_limit = 65535` mysql打开最大文件数

`log-bin=mysql-bin` 这些路径相对于datadir

`binlog_format=mixed` 日志格式

`server-id	= 1` 给服务器分配一个独一无二的ID编号; n的取值范围是1~2的32次方启用二进制日志功能。在复制数据同步的时候会用到，Helloweba后面会有文章介绍。

`expire_logs_days = 10` 启用二进制日志后，保留日志的天数。服务器会自动清理指定天数前的日志文件，如果不设置则会导致服务器空间耗尽。一般设置为7～14天。

`default_storage_engine = InnoDB` 新数据表的默认存储引擎(默认设置是MyISAM)。这项设置还可以通过–default-table-type选项来设置。

`innodb_file_per_table = 1` InnoDB 提供了更灵活的方式，它把每个数据库的信息保存在一个 .ibd 数据文件中。每个 .idb 文件代表它自己的表空间。通过这样的方式可以更快地完成类似 “TRUNCATE” 的数据库操作，当删除或截断一个数据库表时，你也可以回收未使用的空间。这样配置的另一个好处是你可以将某些数据库表放在一个单独的存储设备。这可以大大提升你磁盘的 I/O 负载。

`innodb_data_home_dir = /data/mysql `InnoDB主目录，所有与InnoDB数据表有关的目录或文件路径都相对于这个路径。在默认的情况下，这个主目录就是MySQL的数据目录。

`innodb_data_file_path = ibdata1:10M:autoextend` 用来容纳InnoDB为数据表的表空间: 可能涉及一个以上的文件; 每一个表空间文件的最大长度都必须以字节(B)、兆字节(MB)或千兆字节(GB)为单位给出; 表空间文件的名字必须以分号隔开; 最后一个表空间文件还可以带一个autoextend属性和一个最大长度(max:n)。

`innodb_log_group_home_dir = /data/mysql` 用来存放InnoDB日志文件的目录路径(如ib_logfile0、ib_logfile1等)。在默认的情况下，InnoDB驱动程序将使用 MySQL数据目录作为自己保存日志文件的位置。

`innodb_buffer_pool_size = 128M `这个参数是InnoDB存储引擎的核心参数，默认为128KB，这个参数要设置为物理内存的60%～70%。

`innodb_log_file_size = 32M` 事务日志文件写操作缓存区的最大长度(默认设置是1MB)。

`innodb_log_buffer_size = 8M` 事务日志所使用的缓存区。InnoDB在写事务日志的时候，为了提高性能，先将信息写入Innodb Log Buffer中，当满足innodb_flush_log_trx_commit参数所设置的相应条件（或者日志缓冲区写满）时，再将日志写到文件（或者同步到磁盘）中。可以通过innodb_log_buffer_size参数设置其可以使用的最大内存空间。默认是8MB，一般为16～64MB即可。

`innodb_flush_log_at_trx_commit = 1` 这个选项决定着什么时候把日志信息写入日志文件以及什么时候把这些文件物理地写(术语称为”同步”)到硬盘上。设置值0的意思是每隔一秒写一次日 志并进行 同步，这可以减少硬盘写操作次数，但可能造成数据丢失; 设置值1(设置设置)的意思是在每执行完一条COMMIT命令就写一次日志并进行同步，这可以防止数据丢失，但硬盘写操作可能会很频繁; 设置值2是一般折衷的办法，即每执行完一条COMMIT命令写一次日志，每隔一秒进行一次同步。

`innodb_lock_wait_timeout = 50 `如果某个事务在等待n秒(s)后还没有获得所需要的资源，就使用ROLLBACK命令放弃这个事务。这项设置对于发现和处理未能被InnoDB数据表驱动 程序识别出来的死锁条件有着重要的意义。这个选项的默认设置是50s。

#### 示例：内存4G的my.cnf配置

```
[client]
port        = 3306
socket      = /tmp/mysql.sock

[mysqld]
port        = 3306
socket      = /tmp/mysql.sock
user    = mariadb
basedir = /usr/local/mariadb
datadir = /data/mysql
log_error = /data/mysql/mariadb.err
pid-file = /data/mysql/mariadb.pid
skip-external-locking
key_buffer_size = 64M
max_allowed_packet = 1M
table_open_cache = 256
sort_buffer_size = 1M
net_buffer_length = 8K
read_buffer_size = 1M
read_rnd_buffer_size = 512K
myisam_sort_buffer_size = 16M
thread_cache_size = 32
query_cache_size = 32M
tmp_table_size = 64M

explicit_defaults_for_timestamp = true
#skip-networking
max_connections = 500
max_connect_errors = 100
open_files_limit = 65535

log-bin=mysql-bin
binlog_format=mixed
server-id   = 1
expire_logs_days = 10

default_storage_engine = InnoDB
innodb_file_per_table = 1
innodb_data_home_dir = /data/mysql
innodb_data_file_path = ibdata1:10M:autoextend
innodb_log_group_home_dir = /data/mysql
innodb_buffer_pool_size = 256M
innodb_log_file_size = 64M
innodb_log_buffer_size = 8M
innodb_flush_log_at_trx_commit = 1
innodb_lock_wait_timeout = 50

[mysqldump]
quick
max_allowed_packet = 16M

[mysql]
no-auto-rehash

[myisamchk]
key_buffer_size = 64M
sort_buffer_size = 1M
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout
```