# [数据持久化系列之Mysql](https://www.cnblogs.com/lamdong/p/8431625.html)

**一、命令行操作**

1、显示所有库：

​        show databases;

2、要操作某个库，比如库名：

​        db_book：use db_book;

3、查看表的基本结构，比如表名：

​        t_book：desc t_book;

4、查看某个表的创建sql语句：

​        show create table t_book;

 

**二、数据类型**

1、char和varchar的区别:

​        char是定长的，比如char（10）如果存的2位字符，给该字段分配的空间还是10个字符，varchar（10）存的就是2个字符，char性能好耗空间，varchar空间好耗性能

2、BLOB:

​        如果数据库要存图片和视频，用BLOB数据类型

3、int:

​        10亿级别的数据量是可以承载的，如果大于10亿就要用BIGINT，看项目大小

4、decimal（5,2）：

​        表示有5位数字，其中小数位2位

 

**三、表操作**

1、创建表：

CREATE TABLE `sign_guest` (

  `id` INT(11) NOT NULL AUTO_INCREMENT,

  `realname` VARCHAR(64) NOT NULL,

  `phone` VARCHAR(16) NOT NULL,

  `email` VARCHAR(254) NOT NULL,

  `sign` TINYINT(1) NOT NULL,

  `create_time` TIMESTAMP  NULL  DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间' ,

  `event_id` INT(11) NOT NULL,

  PRIMARY KEY (`id`),

  KEY `sign_guest_event_id_fa7638b3_fk_sign_event_id` (`event_id`),

  CONSTRAINT `sign_guest_event_id_fa7638b3_fk_sign_event_id` FOREIGN KEY (`event_id`) REFERENCES `sign_event` (`id`),

  UNIQUE KEY (`phone`,`event_id`)    -----两个字段联合添加唯一性约束，（应用场景：一场发布会不能有相同的号码，不同的发布会可以有相同的号码）

) ENGINE=INNODB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;

 

2、如何写外键关联两个表

CREATE TABLE t_book(

​        id int primary key auto_increment,   ----auto_increment表示自增，一般声明主键时用到

​        bookName varchar(20),

​        author varchar(10),

​        price decimal(6,2),

​        bookTypeId int,

​        constraint `fk` foreign key (`bookTypeId`) references `t_bookType`(`id`)   ----声明外键，这里不能用单引号和双引号，要用tab键上面的那个键

)

 

3、修改表：

​    1> 修改表给某个字段加上唯一性约束：

​    ALTER TABLE `sign_guest` ADD UNIQUE(`phone`);

​    2> 修改表名：

​    alter table t_book rename t_book2;  ----把t_book表名字从t_book改成t_book2

​    3> 修改字段：

​    alter table t_book change author newAuthor varchar(20);  ---旧字段名author，改成新字段名newAuthor再给新字段名加属性

​    4> 增加字段：

​    alter table t_book add publishDate date not null first; -----加个字段，字段要有数据类型，这里给了date，再给上完整性约束条件（是否主外键，是否自增，是否唯一非空等），first表示在第一行插入

​    alter table t_book add publishDate date not null after price; ---after price表示在price字段后面插入

​    5> 删除字段：

​    alter table t_book drop author;

 

4、查询表：

1> 常用条件有

1) IN 和 NOT IN

2) between A and B

3) Like 和 not like   %匹配多个字符   _匹配单个字符

4) is null 和 is not null

5) distinct

2> 重点是掌握group by:

​    2.1> 跟group_concat()函数一起使用：

​         select gradeName, group_concat(stuName) From t_student group by gradeName; ----根据年级名称分组后把每个年级的学生名字全部列出来

​    2.2> 跟having一起使用：

​        select gradeName , count(stuName) from t_student group by gradeName having  count(stuName)>3;

​    2.3> 使用with rollup在最后加一行合计行

​        select gradeName , count(stuName) from t_student group by gradeName having  count(stuName)>3 with rollup;

 

3> 分页查询：

​    select * from t_student limit 0, 5; ----0是初始位置的下标，表示查第一条数据（id为1），5表示每页多少条

 

4> 连接查询：

​    4.1>内连接：

​        select * from t_book, t_bookType where t_book.bookTypeId=t_bookType.id;

​    4.2> 外连接：

​         select * from t_book left join t_bookType on t_book.bookTypeId = t_bookType.id  -----左连接查出表1的全部的数据，查出表2根据条件匹配的数据

​         select * from t_book right join t_bookType on t_book.bookTypeId = t_bookType.id  ------右连接查出表2的全部的数据，查出表1根据条件匹配的数据

 

5> Exists关键字：

如果子查询查询到有记录，则执行外查询 ，not exist则相反

select * from t_book where exists (select * from t_bookType);

 

6> Any关键字：

​    满足任意一个条件都可以  

​     select * from t_book where price >= any (select price from t_pricelevel); ------子查询查出来的结果是价格的集合，本来不能用>=跟集合来比较，但是加个any后，意思成了跟任意一个集合里的价格进行比较，只要满足 条件的记录外查询都查询出来

 

7> All关键字：

​    要满足所有条件 

​     select * from t_book where price >= all (select price from t_pricelevel); ------子查询查出来的结果是价格的集合，本来不能用>=跟集合来比较，但是加个all后，意思成了跟集合里所有的价格逐个进行比较，要满足所 有条件的记录外查询才查询出来

 

8> union 和 union all：

​    把两条查询语句的查询结果合并，union在合并后会distinct，union all显示合并结果不distinct

 

**四、索引**

​    索引由表中一列或多列组合而成，作用提高查询速度；分为普通索引、唯一性索引（unique）、全文索引（fulltext)、单列索引、多列索引和空间索引(spatial)

1、创建索引：

​    1.1> 创建表的时候创建索引

语法： CREATE TABLE 表名（属性名   数据类型[完整性约束条件]，

​                          .....

​                          [UNIQUE|FULLTEXT|SPATIAL]|INDEX|KEY[别名]（属性名[(长度)][ASC|DESC]));

​        CREATE TABLE t_user （id int,

​                              userName varchar(20),

​                              index(userName)  -----创建单列索引，字段用的是userName

​                              );

​        CREATE TABLE t_user （id int,

​                              userName varchar(20),

​                              unique index index_userName(userName)  -----创建唯一索引，字段用的是userName，同时给userName取了别名index_userName

​                              );

​        CREATE TABLE t_user （id int,

​                              userName varchar(20),

​                              index index_userName_password(userName,password)  -----创建多列索引

​                              );                     

 

​    1.2> 表已经存在，给表创建索引：

​        CREATE index index_userName on t_user(userName);  ----在表t_user的字段userName上创建索引同时取别名 index_userName

​        CREATE unique index index_userName on t_user(userName);  ---在index全面加unique创建唯一索引

​        CREATE index index_userName_password on t_user(userName,password);  ---创建多列索引

 

​    1.3> 更改表的时候创建索引：

​        ALTER TABLE t_user Add index index_userName(userName);  ---创建单列索引，字段用的是userName

​        ALTER TABLE t_user Add unique index index_userName(userName); ---唯一索引

​        ALTER TABLE t_user Add index index_userName_password(userName,password);  ---多列索引

 

2、删除索引：

​    drop index 索引名 on 表名;

 

 

**五、编码**

操作修改默认编码方式

1、显示编码方式：

​    show variables like 'character%';

2、修改编码方式：

​    要保证客户端，服务器和连接过程编码都一样

SET character_set_client = utf8 ; 

SET character_set_connection = utf8 ;   

SET character_set_database = utf8 ;   

SET character_set_results = utf8 ;   

SET character_set_server = utf8 ;

3、重启mysql服务

​    停止：net stop mysql 

​    启动：net start mysql

 

**六、视图**

创建语法：

CREATE [ALGORITHM={UNDEFIEND|MERGE|TEMPTABLE}]

​       VIEW 视图名 [(属性清单)]

​       AS SELECT 语句

​       [WITH [CASCADED|LOCAL] CHECK OPTION];

 

describe 视图名; ---查看视图基本信息

show table status 视图名; ---查看表（包括视图）基本信息

show create view 视图名; ----查看表或视图的详细信息

 

1、修改视图

​    1.1> CREATE OR REPLACE VIEW语句：

CREATE OR REPLACE [ALGORITHM={UNDEFIEND|MERGE|TEMPTABLE}]

​       VIEW 视图名 [(属性清单)]

​       AS SELECT 语句

​       [WITH [CASCADED|LOCAL] CHECK OPTION];

 

​    1.2> ALTER语句

ALTER [ALGORITHM={UNDEFIEND|MERGE|TEMPTABLE}]

​       VIEW 视图名 [(属性清单)]

​       AS SELECT 语句

​       [WITH [CASCADED|LOCAL] CHECK OPTION];

 

2、视图是虚拟的表，数据来源于原表，对视图当作表来执行insert into、update和delete语句操作的是原表的数据

 

3、删除视图：DROP VIEW [IF EXISTS] 视图名列表 [RESTRICT|CASCADE];

 

**七、触发器**

1、创建只有一个执行语句的触发器：

CREATE trigger 触发器名  before | after 触发事件

​            on 表名 for each row 执行语句

 

示例：create trigger trig_book after insert  ------触发事件有insert 、 update和delete，这里用insert触发

​              on t_book for each row

​                  update t_bookType set bookNum = bookNum+1 where new.bookTypeId = t_booktype.id;  -----过渡变量有new 和 old, insert和update操作使用new，表示更新的那条数据，delete操作用old，表示删除的那条数据

 

2、创建有多个执行语句的触发器：

CREATE trigger 触发器名 before | after 触发事件

​            on 表名 for each row

​            begin

​                执行语句

​            end

 

示例：

delimiter |   -----delimiter的作用是防止mysql遇到第一个分号就当作一个语句开始执行

create trigger trig_book2 after delete

​        on t_book for each row

​        begin

​          update t_bookType set bookNum = bookNum -1 where old.bookTypeId = t_bookType.id;

​          insert into t_log values(null,now(),'在book表里删除了一条数据');

​          delete from t_test where old.bookTypeId = t_test.id;

​        end

|

delimiter ;

 

3、查看触发器：

​    3.1> show triggers;  

​    3.2> 在系统库information_schema的triggers表里面查看

 

4、删除触发器：

​    drop trigger 触发器名 ; 

 

**八、Mysql常用函数**

1、日期和时间函数：

​        curdate() ---返回当前日期

​        curtime() --返回当前时间

​        month(date) ---返回date中的月份，范围是1~12

2、字符串函数 ： 

​        char_length(str) --返回字符串str长度

​        upper(str)   ---返回str全部大写

​        lower(str)  ---返回str全部小写

3、数学函数： 

​        ABS(X)  ---求X的绝对值

​        SQRT(x)  ---求x的平方根

​        MOD(x) ---求x的余数

4、加密函数：

​        PASSWORD(str) ---一般对用户的密码加密  不可逆

​        MD5(str)   ---普通加密  不可逆

​        ENCODE(str, pswd_str)  ---加密函数，结果是一个二进制数，必须使用BLOB类型的字段来保存它

​        DECODE(crypt_str,pswd_str) ---解密函数   加解密要用同一个pswd_str

 

**九、存储过程和函数**

1、创建存储过程：

CREATE PROCEDURE sp_name ([proc_parameter[,...]])

​    [characteristic ...] routine_body

 

存储过程示例：

delimiter &&

create procedure pro_book (in bT int, out count_num int)

​       reads sql data

​       begin

​         select count(*) from t_book where bookTypeId=bT;

​       end

​       &&

delimiter;

 

call procedure(1,@total)  ----调用存储过程，@total指明是全局变量，不指明就是局部变量

 

2、创建函数：

CREATE FUNCTION sp_name ([func_parameter[,...]])

​    RETURNS type

​    [characteristic ...] routine_body

 

​    proc_parameter:

​    [ IN | OUT | INOUT ] param_name type

 

​    func_parameter:

​    param_name type

 

type:

​    Any valid MySQL data type

 

characteristic:

​    LANGUAGE SQL

  | [NOT] DETERMINISTIC

  | { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }

  | SQL SECURITY { DEFINER | INVOKER }

  | COMMENT 'string'

 

routine_body:

​    Valid SQL procedure statement or statements

 

函数示例：

delimiter &&

create procedure func_book (bookId, int)

​       returns varchar(20)

​       begin

​         return (select bookName from t_book where id=bookId);

​       end

​       &&

delimiter;

 

select func_book(1);  ---调用函数

 

 

3、查看存储过程和函数

​    3.1> show status语句查看状态

​    show {procedure|function} status [like 'pattern'];

​    3.2> show create语句查看定义

​    show create {procedure|function} sp_name;

 

4、删除存储过程和函数

​    drop {procedure|function} sp_name;

 

**十、变量**

1、变量的使用：

DECLARE var_name[,.....] type[default value]

示例：

delimiter&&

CREATE PROCEDURE pro_user()

​    BEGIN

​      declare a, b varchar(20);

​      insert into t_user values(null, a, b);

​    END

&&

delimiter ;

 

2、为变量赋值：

​    2.1> 方式1：

​    SET var_name = expr[,var_name=expr]...

​    示例：

delimiter&&

CREATE PROCEDURE pro_user()

​    BEGIN

​      declare a, b varchar(20);

​      set a='good good study', b='day day up';

​      insert into t_user values(null, a, b);

​    END

&&

delimiter ;

 

​    2.2> 方式2：

​    SELECT col_name[,...] INTO var_name[,...] FROM table name where condition

​    示例：

delimiter&&

CREATE PROCEDURE pro_user()

​    BEGIN

​      declare a, b varchar(20);

​      select userName2,password2 into a, b from t_user2 where id2=1;

​      insert into t_user values(null, a, b);

​    END

&&

delimiter ;

 

**十一、游标**

1、使用过程为声明--》打开---》使用---》关闭

​    1.1> 声明：

​    DECLARE cursor_name CURSOR FOR select_statement;

​    1.2> 打开：

​    OPEN cursor_name;

​    1.3> 使用：

​    FETCH cursor_name INTO var_name[,var_name.....];

​    1.4> 关闭游标：CLOSE cursor_name;

 

2、示例：

delimiter&&

CREATE PROCEDURE pro_user()

​    BEGIN

​      declare a, b varchar(20);

​      declare cursor_user_2 cursor for select userName2,password2 from t_user2;   ---声明游标

​      open cursor_user_2;                                                         ---打开游标

​      fetch cursor_user_2 into a, b;                                              ---通过游标取数据赋值给变量

​      insert into t_user values(null, a, b);

​      close cursor_user_2;

​    END

&&

delimiter ;

 

**十二、流程控制**

1、IF语句：

IF search_condition THEN statement_list

​      [ELSEIF search_condition THEN statement_list]...

​      [ELSEIF statement_list]

END IF

 

示例：

delimiter &&

create procedure pro_user5(IN bookId int)

​       begin

​       select count(*) into @num from t_user where id = bookId;

​       if @num>0 then update t_user set userName='lower' where id= bookId;

​       else

​          insert into t_user values(null,'hkdfd','dsfs');

​        end if;

​       end

&&

delimiter ;

 

2、CASE语句：

CASE case_value

​    WHEN when_value THEN statement_list

​    [WHEN when_value THEN statement_list]....

​    [ELSE statement_list]

END CASE

 

示例：

delimiter &&

create procedure pro_user5(IN bookId int)

​       begin

​       select count(*) into @num from t_user where id = bookId;

​       case @num

​            where 1 then update t_user set userName='lower' where id= bookId;

​            where 0 then insert into t_user values(null,'hkdfd','dsfs');

​            else insert into t_user values(null,'hkdfd','dsfs');

​       end case;

​       end

&&

delimiter ;

 

3、LOOP, LEAVE语句

[begin_label:]LOOP

​     statement_list

END LOOP[end_label]

 

LEAVE label   ----跳出循环

 

示例：

delimiter &&

create procedure pro_user5(IN totalNum int)

​       begin

​       loop1:loop

​          set totalNum=totalNum-1;

​          if totalNum=0 then leave loop1;

​          else insert into t_user values(totalNum,'hkdfd','dsfs');

​          end if;

​       end loop loop1;

​       end

&&

delimiter ;

 

4、iterate语句 跳出本次循环，直接执行下一次循环

delimiter &&

create procedure pro_user5(IN totalNum int)

​       begin

​       loop1:loop

​          set totalNum=totalNum-1;

​          if totalNum=0 then leave loop1;

​          elseif totalNum=3 then iterate loop1;

​          end if;

​          insert into t_user values(totalNum,'hkdfd','dsfs');

​       end loop loop1;

​       end

&&

delimiter ;

 

5、repeat语句：重复执行语句直到满足某个条件后跳出循环

[begin_label:]repeat

​            statement_list

​            until search_condition

end repeat[end_label]

 

示例：

delimiter &&

create procedure pro_user5(IN totalNum int)

​       begin

​       repeat

​       set totalNum=totalNum-1;

​       into t_user values(totalNum,'hkdfd','dsfs');

​       until totalNum=1;

​       end repeat;

​       end

&&

delimiter ;

 

6、 while语句:

[begin_label]while search_condition do

​    statement_list

end while[end_label]

 

示例：

delimiter &&

create procedure pro_user5(IN totalNum int)

​       begin

​       while totalNum>0 do

​       into t_user values(totalNum,'hkdfd','dsfs');

​       set totalNum=totalNum-1;

​       end while;

 

​       end

&&

delimiter ;

 

**十三、Mysql设置时间类型的字段默认值为系统时间的方法**

1、方法一：把字段类型设置为timestamp，再把值设置成默认的current_timestamp，示例如下

​    1.1> 添加CreateTime 设置默认时间 CURRENT_TIMESTAMP

ALTER TABLE `table_name`

ADD COLUMN  `CreateTime` timestamp NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间' ;

 

​    1.2> 修改CreateTime 设置默认时间 CURRENT_TIMESTAMP

ALTER TABLE `table_name`

MODIFY COLUMN  `CreateTime` timestamp NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间' ;

 

​    1.3> 添加UpdateTime 设置 默认时间 CURRENT_TIMESTAMP   设置更新时间为 ON UPDATE CURRENT_TIMESTAMP (修改记录时，该字段值自动更新为系统时间)

ALTER TABLE `table_name`

ADD COLUMN `UpdateTime` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '创建时间' ;

 

​    1.4> 修改 UpdateTime 设置 默认时间 CURRENT_TIMESTAMP   设置更新时间为 ON UPDATE CURRENT_TIMESTAMP

ALTER TABLE `table_name`

MODIFY COLUMN `UpdateTime` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '创建时间' ;

 

2、方法二：使用触发器

​    create table test_time (   

​     id int(11),   

​     create_time datetime   

​    );   

​    delimiter |   

​    create trigger default_datetime before insert on test_time   

​      for each row   

​        if new.create_time is null then   

​          set new.create_time = now();   

​        end if;|   

​    delimiter ;   

 

**十四、数据备份和还原**

1、数据备份

方式一：使用mysqldump命令备份（命令行切换目录到mysql安装目录的bin目录，该目录下有mysqldump.exe可执行文件）

mysqldump -u username -p dbname table1,table2...  > BackUpName.sql

方式二：使用sqlyon图形工具

 

2、数据还原

方式一：mysql -u username -p [dbname] < backup.sql

 

**十五、Mysql数据库的各种事务**

1、SET AUTOCOMMIT=0     -----取消事务自动提交处理，开启事务处理

2、SET AUTOCOMMIT=1     -----打开事务自动提交处理，关闭事务处理

3、START TRANSACTION    -----启动事务

4、BEGIN                -----启动事务， 跟START TRANSACTION 相当

5、COMMIT               -----提交事务

6、ROLLBACK             -----回滚全部事务

7、SAVEPOINT 事务保存点名称      --------设置事务保存点

8、ROLLBACK TO SAVEPOINT 保存点名称    ---------回滚操作到保存点

 

**十六、安装卸载**

1、安装比较简单，注意安装mysql时字符编码格式要选择utf8

2、完全卸载mysql方法：

​    2.1>先在程序管理卸载mysql，

​    2.2>在regedit注册表以下两个路径把mysql文件夹内容删除：

​    HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\Eventlog\Application\MySQL

​    HKEY_LOCAL_MACHINE\SYSTEM\ControlSet002\Services\Eventlog\Application\MySQL

​    2.3>删除安装目录下所有文件 

​    2.4> 删除C:\ProgramData\MySQL文件夹下文件