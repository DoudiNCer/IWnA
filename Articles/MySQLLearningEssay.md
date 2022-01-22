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

## MySQL专业术语



## 数据操作

## 数据结构

## 用户权限

