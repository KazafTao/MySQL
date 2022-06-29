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

变长字符串。真实数据有多少个字符串就按多少个字符串来存储。但不能超过定义的最大值。

```sql
varchar(10)    -- 最多用10个字符来存储，如果真实数据小于10个字符，则按真实数据的字符个数来分配存储空间
```

#### char

定长字符串

```sql
char(10)  -- 固定用10个字符来存储
```

#### text

#### mediumtext

#### longtext

### 整数

#### tinyint

有符号，取值范围： -128 ~ 127

无符号，取值范围： 0 ~ 255

#### int

有符号，取值范围:  -2^31^ ~ 2^31^ -1

无符号，取值范围： 0 ~ 2^32^

#### bigint

有符号，取值范围：-2^63^  ~ 2^63^  -1 

无符号，取值范围： 0 ~ 2^64^

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

#### 查看表中数据

```sql
select * from 表名
```

#### 创建数据表

```sql
create table 表名 (
    id int auto_increment primary key,            -- 数据类型为整形,且为主键(数值唯一且不允许为空),内部维护，自增
    name varchar(16) not null,                    -- 数据类型为字符型，不允许为空
    age tinyint unsigned default 3                -- 不存储负数，插入数据时，默认为3,取值范围为 0 ~ 255
) default charset=utf8;
```

#### 插入数据

```sql
insert into 表名(指定列名1，指定列名2) values(值1，值2),(值1，值2)
```

#### 删除数据表

```sql
drop table 表名
```

#### 显示表结构

```sql
desc 表名
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
