# mysql-postgresql
Mysql 快速迁移到PGsql (基于mysql2pg程序)

最近需要将一个Mysql数据库迁移到PGsql，网上搜了一些方法，实践后要么行不通，要么效果不好。

最后发现基于perl 语言的 mysql2pg 程序效果还不错，特此实践后整理了下：



【Linux安装perl】

wget https://www.cpan.org/src/5.0/perl-5.28.1.tar.gz



【下载perl转换程序】

https://sourceforge.net/projects/mysql2pg/

具体下载链接：

http://sourceforge.net/projects/mysql2pg/files/OldFiles/mysql2pg.tar.gz/download

 

【解压】

tar -zxvf mysql2pg.tar.gz

 

【运行】

语法：

mysql2pg.pl -u user -p -h host -d database [-t table] OPTIONS

 

示例：

perl mysql2pg.pl -u root -p root -h 10.168.5.157 -d mysql_test

以上连接信息为mysql数据库，修改其中的数据库连接信息，包括数据库名mysql_test

 

成功后会将创建的表打印到控制台，复制创建语句到 pgsql 数据库中执行即可。





可能会出现的问题：Can't locate DBI.pm

解决方法：

运行 yum -y install perl-DBD-MySQL  





插入语句

插入语句这块，pgsql 可以直接执行 mysql 插入语句，无需修改。但序列有所不同：

方法一：创建序列（推荐使用）

CREATE SEQUENCE seq_sys_user

START WITH 1001

INCREMENT BY 1

NO MINVALUE

NO MAXVALUE

CACHE 1;



注：这里可以调大序列的开始值，不同表可以有所不同，具体要看表数据最大id值，原则是要比最大id值大，避免在新插入数据时id重复导致报错。

修改自增主键ID:

alter table sys_user alter column user_id set default nextval('seq_sys_user');

插入示例：

insert into tablename values( nextval('seq_sys_user'), 'name')

备注：使用序列方式在数据库迁移时，可以调大创建序列的开始值。

 



方法二：使用SERIAL

CREATE TABLE users

(

id SERIAL primary key ,

name character varying

)

备注：使用SERIAL方式使用方便，但在数据库迁移时，自增id将从1开始，不利于数据库迁移。



------------------THE END
