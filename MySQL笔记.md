## 参考
 [21分钟MySQL基础入门](https://github.com/jaywcjlove/mysql-tutorial/blob/master/21-minutes-MySQL-basic-entry.md)

#基础

* 创建用户、授权、[删除用户](https://www.cnblogs.com/chanshuyi/p/mysql_user_mng.html)

``` sql
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
eg:
CREATE USER 'dog'@'localhost' IDENTIFIED BY '123456'; 
CREATE USER 'pig'@'192.168.1.101_' IDENDIFIED BY '123456'; 
CREATE USER 'pig'@'%' IDENTIFIED BY '123456'; 
CREATE USER 'pig'@'%' IDENTIFIED BY ''; 
CREATE USER 'pig'@'%';

链接：https://www.jianshu.com/p/3d7be4cbd536
CREATE USER 'www-data'@'localhost' IDENTIFIED BY 'www-data'; 


grant all privileges on awesome.* to 'www-data'@'%' identified by 'www-data';
flush privileges;
```

* 登录数据库

``` sql
PeterPoker:blog wangchao$ mysql -u root -p

Enter password: （输入密码，默认为空，直接Enter下一步）
CREATE DATABASE blog CHARACTER SET utf8;

```

* 创建数据库: 

``` sql
CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name [DEFAULT] CHARACTER SET [=] charset_name 
eg:
CREATE DATABASE t1;
CREATE DATABASE IF NOT EXISTS t1;
CREATE DATABASE IF NOT EXISTS t2 CHARACTER SET gbk;
```
* 修改数据库: 

``` sql
ALTER {DATABASE | SCHEMA} [db_name] [DEFAULT] CHARACTER SET [=] charset_name 
eg:
修改字符编码方式
ALTER DATABASE databasename CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
ALTER TABLE tablename CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
#Or if you're still on MySQL 5.5.2 or older which didn't support 4-byte UTF-8, use utf8 instead of utf8mb4:
ALTER DATABASE databasename CHARACTER SET utf8 COLLATE utf8_unicode_ci;
ALTER TABLE tablename CONVERT TO CHARACTER SET utf8 COLLATE utf8_unicode_ci;
```
* MySQL提示符: 

``` 
\D 完整的日期
\d 当前数据库
\h 服务器名称
\u 当前用户
```
* 查看当前服务器下的数据库列表: 

``` sql
SHOW {DATABASES | SCHEMA} [LIKE 'pattern' | WHERE expr];
eg:
SHOW DATABASES;
```
* 打开数据库: 

``` sql
USE 数据库名称;
eg:
USE test1;
```

* 关闭数据库: 

``` sql
mysql> exit;
```

* 创建数据库表: 

``` sql
CREATE TABLE [IF NOT EXISTS] table_name(
	column_name data_type,
	...
);
eg:
CREATE TABLE tb1(
	username VARCHAR(20),
	age TINYINT UNSIGNED,
	salary FLOAT(8,2) UNSIGNED
);
```
* 查看数据表结构: 

``` sql
SHOW COLUMNS FROM tbl_name;
eg:
SHOW COLUMNS FROM tb1;
```

* 插入记录: 

``` sql
INSERT [INFO] tbl_name [(col_name,...)] VALUES(val,...);
eg:
INSERT tb1 VALUES('Tom',25,7800.12);
INSERT tb1 (username,salary) VALUES('Tom',7599.5);
```

* 记录查找

``` sql
SELECT expr,... FROM tbl_name
eg:
//查找全部
SELECT * FROM tb1;
```

* 主键

``` sql
SELECT expr,... FROM tbl_name
eg:
//查找全部
CREATE TABLE tb2( username VARCHAR(20) NOT NULL, id SMALLINT UNSIGNED PRIMARY KEY);
```


#Tips

## command not found

``` bash
PeterPoker:~ wangchao$ mysql
-bash: mysql: command not found

```
原因，MacOS上MySQL的dmg安装包把程序安装到了`/usr/local/mysql/bin/`下

查看系统环境变量：
`vi ~/.bash_profile`
会发现此文件不包含`/usr/local/mysql/bin/`

* 解决方法1：

``` bash
vi ~/.bash_profile
i
添加一行：export PATH="/usr/local/mysql/bin:$PATH"
完成！
```

* 解决方法2：
 
创建软连接，命令如下：

``` bash
sudo ln -s /usr/local/mysql/bin/* /usr/bin

小科普：
ln的链接分软链接和硬链接两种：
1、软链接就是：“ln –s 源文件 目标文件”，只会在选定的位置上生成一个文件的镜像，不会占用磁盘空间，类似与windows的快捷方式。
2、硬链接ln源文件目标文件，没有参数-s， 会在选定的位置上生成一个和源文件大小相同的文件，无论是软链接还是硬链接，文件都保持同步变化。
```




[Mac 下 MySQL 的 root 密码忘记](http://www.jianshu.com/p/47caaf07fdfa)