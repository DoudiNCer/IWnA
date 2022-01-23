# MySQL学习笔记

## MySQL介绍

&emsp;&emsp;MySQL是一种流行的**关系型数据库管理系统**(RDBMS)，除NySQL以外，还有其他数据库管理系统：

- **关系型数据库**：SQL Server,SQLite...
- **非关系型数据库**：MongoDB.Redis...

&emsp;&emsp;在Orcale收购MySQL后，有将其闭源的风险，因此原开发团队开发了其分支版本——MariaDB并全面兼容MySQL 5.x。

## MySQL在Linux下的安装和初始化

### MySQL的组成

> - **MySQL** - MySQL服务器程序
> - **MySQL-client** - MySQL 客户端
> - **MySQL-devel** - ~~库和包含文件，如果你想要编译其它MySQL客户端，例如Perl模块，则需要安装该RPM包。~~ 这个玩过Linux的应该都知道
> - **MySQL-shared** - 该软件包包含某些语言和应用程序需要动态装载的共享库(libmysqlclient.so*)。
> - **MySQL-bench** - MySQL数据库服务器的基准和性能测试工具。

### MySQL的安装（RHEL系）

（自觉用root，不会的命令自觉man一下）

下载安装：

```shell
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
rpm -ivh mysql-community-release-el7-5.noarch.rpm
yum update
yum install mysql-server
```

权限配置：

```shell
chown -R mysql:mysql /var/lib/mysql
```

初始化及启动：

```shell
mysqld --initialize
systemctl start mysqld
systemctl enable mysqld		# 酌情使用
mysqladmin --version		# 验证安装
```

用户登录：

```shell
mysql -h server -P port(default 3306) -u username -p password
```

## MySQL专业术语

> - **数据库:** 数据库是一些关联表的集合。
> - **数据表:** 表是数据的矩阵。在一个数据库中的表看起来像一个简单的电子表格。
> - **列:** 一列(数据元素) 包含了相同类型的数据, 例如邮政编码的数据。
> - **行：**一行（元组，或记录）是一组相关的数据，例如一条用户订阅的数据。
> - **冗余**：存储两倍数据，冗余降低了性能，但提高了数据的安全性。
> - **主键**：主键是唯一的。一个数据表中只能包含一个主键。你可以使用主键来查询数据。
> - **外键：**外键用于关联两个表。
> - **复合键**：复合键（组合键）将多个列作为一个索引键，一般用于复合索引。
> - **索引：**使用索引可快速访问数据库表中的特定信息。索引是对数据库表中一列或多列的值进行排序的一种结构。类似于书籍的目录。
> - **参照完整性:** 参照的完整性要求关系中不允许引用不存在的实体。与实体完整性是关系模型必须满足的完整性约束条件，目的是保证数据的一致性。

## 数据操作

#### 库的操作

```mysql
create database databaseName 
```



## 数据结构

## 用户操作与权限

SQL方法：编辑mysql\user

非SQL方法：

#### 用户操作

```mysql
create user username@ip identified by password;	# ip的通配符为%
drop user username@ip;
rename user old_username@ip to new_username@ip;
```

#### 权限管理

```mysql
show grants for username@ip;
grant permissions on databases.tables to username@ip;
revoke permissions on databases.tables from username@ip;
```
