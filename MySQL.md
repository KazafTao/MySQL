# MySQL

## 概述

mysql是最流行的数据库管理系统，在web应用方面，mysql是最好用的关系型数据库管理系统之一。

数据库（Database）是按照数据结构来组织、存储和管理数据的仓库。

数据库中数据以数据表的形式存储。

## 部署MySQL

### Windows (待补充)

点击[链接](https://cdn.mysql.com//Downloads/MySQLInstaller/mysql-installer-community-8.0.29.0.msi)下载MySQL 8.0.29 msi installer

### Linux

#### 通过docker部署mysql

```shell
systemctl start docker
docker pull mysql
docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
```

## 配置MySQL

### 修改配置文件

配置文件路径 /etc/mysql/my.cnf或者/etc/my.cnf，也可以通过find查找

```
port = 3306  //配置mysql端口为3306
datadir = /var/lib/mysql   //指定了 MySQL 的数据库文件放在/var/lib/mysql路径下
```

### 设置用户密码

```shell
set password = password("psd")
```

## 连接MySQL

### Windows

下载安装dbeaver

配置数据库连接，如果提示 **Public Key Retrieval is not allowed**，则配置驱动属性 allowPublicKeyRetrieval为Yes

![image-20220625174722510](https://s2.loli.net/2022/06/25/BtaAS2V7o3bQTJx.png)

### Linux

```shell
mysql -h 127.0.0.1 -P 3306 -u root -p
```

## MySQL数据类型

### 字符串

#### varchar

变长字符串。真实数据有多少个字符串就按多少个字符串来存储。但不能超过定义的最大值。可以节省空间

```sql
varchar(10)    -- 最多用10个字符来存储，如果真实数据小于10个字符，则按真实数据的字符个数来分配存储空间
```

#### char

定长字符串，读写速度比变长字符串要快。

```sql
char(10)  -- 固定用10个字符来存储
```

#### text

一般用于保存变长的大字符串，最多2<sup>16</sup> - 1个字符。常用于保存长文本，如文章，新闻等。 

#### mediumtext

和text类似，但是最大值为2<sup>24</sup>-1个字符

#### longtext

和text类似，但是最大值为2<sup>32</sup>-1个字符

### 整数

#### tinyint

有符号，取值范围： -128 ~ 127

无符号，取值范围： 0 ~ 255

#### int

有符号，取值范围:  -2<sup>31</sup> ~ 2<sup>31</sup> -1

无符号，取值范围： 0 ~ 2<sup>32</sup>

#### bigint

有符号，取值范围：-2<sup>63</sup>  ~ 2<sup>63</sup>  -1 

无符号，取值范围： 0 ~ 2<sup>64</sup>

### 浮点数

#### decimal

精确的小数值，通常使用如下形式decimal(m,d)，m最大取值65，d最大取值30。demo如下，

```sql
create table tb1 (
    id int auto_increment primary key,  
   	salary decimal(8,2)                -- 最多8位数，小数点后最多2位，如果插入的数据超过2位小数，则只保留2位小数(四舍五入)
) default charset=utf8;
```

#### float

不精准

#### double

不精准

### 时间

#### datetime

存储年月日时分秒

#### date

存储年月日

 ## SQL语句

### 数据库操作

#### 查看已有的数据库

```sql
show databases; //显示所有数据库
```

#### 切换到指定的数据库

```sql
use mysql;      //切换到指定数据库
```

#### 创建数据库

```sql
create database 数据库名 default charset utf8 collate utf8_general_ci
```

#### 删除数据库

```sql
drop database 数据库名
```

### 数据表操作

#### 查看数据表

```sql
show tables;
```

#### 创建数据表

```sql
create table 表名 (
    id int auto_increment primary key,            -- 数据类型为整形,且为主键(数值唯一且不允许为空),内部维护，自增
    name varchar(16) not null,                    -- 数据类型为字符型，不允许为空
    age tinyint unsigned default 3                -- 不存储负数，插入数据时，默认为3,取值范围为 0 ~ 255
) default charset=utf8;
```

#### 删除数据表

```sql
drop table 表名
```

#### 显示表结构

```sql
desc 表名
```

#### 修改表结构

```sql
alter 表名 add 列名 列定义  -- 向指定表添加新列
```

### 数据行操作

#### 插入数据

```sql
insert into 表名 (指定列名1，指定列名2) values(值1，值2),(值1，值2)
```

#### 删除数据

```sql
delete from 表名;  -- 删除表中的所有数据
delete from 表名 where 条件  -- 只删除符合条件的数据
```

#### 修改数据

```sql
update 表名 set 列 = 值,列 = 值 -- 将表中所有列的值都统一
update 表名 set 列 = 值 where 条件  -- 只修改表中符合条件的数据的值
```

#### 查看表中数据

```sql
select * from 表名  -- 查询表中所有数据
select 列1, 列2 from 表名  -- 查询表中所有数据的特定字段而不是全部字段
select * from 表名 where 条件  -- 只查询表中符合条件的数据
```

## 在项目中的应用

一般先创建数据库和表结构，表中的数据由程序进行增删改查

### Python

模块：pymysql

#### 建立链接

```python
# 建立链接
with pymysql.connect(host="192.168.98.130", port=3306, user="root", password="123456", db="employee",
                     charset="utf8") as con:
    # 获取游标，游标可以看作是sql查询结果的一个指针
    with con.cursor() as cursor:
        # 执行sql语句
```

#### 执行sql语句

```python
# 执行sql语句
# 插入数据
sql = "insert into admin(username,password,mobile) values(%s,%s,%s)"   
cursor.execute(sql, ["wade", "xxx","123456789"])
# 删除数据
sql = "delete from admin where id = %s"
cursor.execute(sql,["1"])
# 修改数据
sql = "update admin set mobile = %s where id = %s"
cursor.execute(sql,["12345678954","1"])
# 查询数据
cursor.execute("select * from admin")   # 执行查询语句需要获取查询到的数据
data_list = cursor.fetchall()
# cursor.fetchone()    # 用于校验数据库中是否存在某一条数据
print(data_list)     # ((1, 'wade', '1234', 'xxxx'), (2, 'wade', 'xxx', '123456789'))，默认返回元组
```

#### 提交执行

```python
con.commit()
```

### SQL语言
#### 批量创建模拟数据

```sql
-- 清除同名存储过程
drop procedure if exists mock;
DELIMITER $$
-- 创建存储过程
CREATE PROCEDURE mock (n INT ) 
BEGIN
	DECLARE i INT DEFAULT 0;
	declare mobile char(11);
	SET autocommit = 0;
	-- 循环mock数据
		REPEAT
			SET i = i + 1;
			# repeat sql
-- 				随机生成电话号码
			set mobile = concat('1',substring(cast(3 + (rand() * 10) % 7 AS char(50)), 1, 1),right(left(trim(cast(rand() AS char(50))), 11), 9));
			INSERT into employee_manage_mobile(`mobile`,`level` ,`status`,`price`) values (mobile ,ceiling(rand()*3) ,ceiling(rand()*2) ,ceiling(rand()*100));
			UNTIL i = n 
		END REPEAT;
	-- 提交mock数据
	COMMIT;
	SET autocommit = 1;
END 
$$

```

## Trouble shotting

### 忘记密码

默认情况下，启动MySQL时，需要用户输入账号名、密码，如果MySQL正在运行，需要先停止MySQL()

```shell
service mysql stop
```

修改MySQL配置文件(/etc/mysql/my.cnf)

添加```skip-grant-tables = 1```

重新启动MySQL(无账号模式)

```shell
mysql -u root -p
set password = password("psd")
exit;
```

修改MySQL配置文件，重新启动MySQL(需要账号的模式)
